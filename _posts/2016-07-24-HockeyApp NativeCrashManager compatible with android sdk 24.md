---
layout: post
title: HockeyApp NativeCrashManager compatible with android sdk 24
description: "HockeyApp NativeCrashManager compatible with android sdk 24"
modified: 2016-07-24
tags: [hockeyapp, native, crash, manager, android, sdk, 24]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

If you use HockeyApp platform for tracking android native side crashes, you probably aware of <a href="https://github.com/ashtom/breakapp/blob/master/src/net/hockeyapp/android/NativeCrashManager.java">NativeCrashManager</a> which implemented by using apache network library and perfectly working until android sdk version 24. Google has removed apache network library related codes from it's core android module and recommends HttpURLConnection usage as you can read details in the following page :

<a href="https://developer.android.com/about/versions/marshmallow/android-6.0-changes.html#behavior-apache-http-client">https://developer.android.com/about/versions/marshmallow/android-6.0-changes.html#behavior-apache-http-client</a>

I have reimplemented the same logic using okhttp library and it's tested on live with one of our tope games. Here it is :

{% highlight java %}

import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Date;
import java.util.UUID;

import android.net.Uri;
import com.squareup.okhttp.*;
import android.app.Activity;
import android.util.Log;

public class NativeCrashManager {

  private static final String DESCRIPTION_FILE_NAME = "description.txt";
  private static final String TAG = "HockeyApp";

  public static void handleDumpFiles(Activity activity, String identifier, String userId) {
    String[] filenames = searchForDumpFiles();
    for (String dumpFilename : filenames) {
      Log.d(TAG, "dumpFile " + dumpFilename);
      String logFilename = createLogFile();
      if (logFilename != null) {
        uploadDumpAndLog(activity, identifier, dumpFilename, logFilename, userId);
      }
    }
  }

  public static String createLogFile() {
    final Date now = new Date();

    try {
      // Create filename from a random uuid
      String filename = UUID.randomUUID().toString();
      String path = Constants.FILES_PATH + "/" + filename + ".faketrace";
      Log.d(TAG, "Writing unhandled exception to: " + path);

      // Write the stacktrace to disk
      BufferedWriter write = new BufferedWriter(new FileWriter(path));
      write.write("Package: " + Constants.APP_PACKAGE + "\n");
      write.write("Version: " + Constants.APP_VERSION + "\n");
      write.write("Android: " + Constants.ANDROID_VERSION + "\n");
      write.write("Manufacturer: " + Constants.PHONE_MANUFACTURER + "\n");
      write.write("Model: " + Constants.PHONE_MODEL + "\n");
      write.write("Date: " + now + "\n");
      write.write("\n");
      write.write("MinidumpContainer");
      write.flush();
      write.close();

      return filename + ".faketrace";
    }
    catch (Exception another) {
      Log.e(TAG, "File couldn't be write to path!!!", another);
    }

    return null;
  }

  public static void uploadDumpAndLog(final Activity activity, final String identifier, final String dumpFilename, final String logFilename) {
    new Thread() {
      @Override
      public void run() {
        try {

          OkHttpClient httpClient = new OkHttpClient();

          File dumpFile = new File(Constants.FILES_PATH, dumpFilename);
          File logFile = new File(Constants.FILES_PATH, logFilename);

          MultipartBuilder multipartBuilder = new MultipartBuilder()
                  .type(MultipartBuilder.FORM)
                  .addFormDataPart("attachment0", dumpFile.getName(), RequestBody.create(MediaType.parse("text/plain"), dumpFile))
                  .addFormDataPart("log", logFile.getName(), RequestBody.create(MediaType.parse("text/plain"), logFile))
                  ;

          RequestBody requestBody = multipartBuilder.build();
          Request request = new Request.Builder()
                  .url("https://rink.hockeyapp.net/api/2/apps/" + identifier + "/crashes/upload")
                  .post(requestBody)
                  .build();

          Response response = httpClient.newCall(request).execute();
          if (response.isSuccessful()) {
            Log.d(TAG,"result code : " + response.code());
            Log.d(TAG, "Upload response : " + response.message());
          } else {
            Log.d(TAG, "Failed to upload dumpFile caused by code : " + response.code());
          }

        }
        catch (Exception e) {
          Log.e(TAG, "Failed to upload dumpFile", e);
        }
        finally {
          activity.deleteFile(logFilename);
          activity.deleteFile(dumpFilename);
        }
      }

      private void fillCrashDescription(File descriptionFile) {
        FileWriter fileWriter = null;
        try {
          fileWriter = new FileWriter(descriptionFile);
          fileWriter.write(SessionLogManager.getSavedSessionData());
        } catch (IOException e) {
          Log.e(TAG, "description file error", e);
        } finally {
          if(fileWriter != null) {
            try {
              fileWriter.close();
            } catch (IOException e) {
              Log.e(TAG, "fileWriter couldn't be closed!", e);
            }
          }
        }
      }

    }.start();
  }

  private static String[] searchForDumpFiles() {
    if (Constants.FILES_PATH != null) {
      // Try to create the files folder if it doesn't exist
      File dir = new File(Constants.FILES_PATH + "/");
      boolean created = dir.mkdir();
      if (!created && !dir.exists()) {
        return new String[0];
      }

      // Filter for ".dmp" files
      FilenameFilter filter = new FilenameFilter() {
        public boolean accept(File dir, String name) {
          return name.endsWith(".dmp");
        }
      };
      return dir.list(filter);
    }
    else {
      Log.d(TAG, "Can't search for exception as file path is null.");
      return new String[0];
    }
  }
}

{% endhighlight %}