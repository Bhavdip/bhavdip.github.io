---
layout: post
title:	Introducing the Transfer Utility for the AWS SDK for Android Part2
image:	byword-icon.png
---

In previous post [Part1]({% post_url 2016-02-27-1_AWS_S3_Transfer_Manger_Utility %}) we see the basic introduction of Transfer Manager to Transfer Utility. You can try the sample source at end of post, try to explain and finish this post.
{{ more }}

**<span style="color:#c60">To start using S3 in your application, you need to do the following:</span>**

* Add the correct import statements
* Declare S3 Transfer Utility Service into your `AndroidManifest.xml`
* Instantiate the Cognito Caching Credentials provider, an Amazon S3 client, and Transfer Utility
* Define the Service in `AndroidManifest.xml`

**<span style="color:#c60">Add following declaration into your android manifest file:</span>**

	<service
	  android:name="com.amazonaws.mobileconnectors.s3.transferutility.TransferService"
	  android:enabled="true" />

**<span style="color:#c60">Instantiate the AWS S3 client:</span>**

Pass your credentials provider to the S3 client constructor. Then set the region on the client, like so.

	// Create an S3 client
	AmazonS3 s3 = new AmazonS3Client(credentialsProvider);

**<span style="color:#c60">Instantiate the Transfer Utility:</span>**

You can use the `TransferUtility` class to upload or download the files from S3 Bucket. Pass the application context and S3 client like so:

	TransferUtility transferUtility = new TransferUtility(s3, APPLICATION_CONTEXT);

**<span style="color:#c60">Operations</span>**

Before we start using S3 bucket you must familiar with yourself with the following concepts:

`MY_BUCKET-` A string representing the name of the S3 bucket where the file is stored.

`OBJECT_KEY` A string representing the name of the S3 object (a file in this case) to download.

`MY_FILE`  The java.io.File object where the download/upload file will exist.

**<span style="color:#007697">Upload an Object to S3</span>**

To upload a file:

	TransferObserver observer = transferUtility.upload(
	  MY_BUCKET,     /* The bucket to upload to */
	  OBJECT_KEY,    /* The key for the uploaded object */
	  MY_FILE        /* The file where the data to upload exists */
	);

Uploads automatically used the S3’s multipart upload functionality on large file to enhance throughput.

**<span style="color:#007697">Upload an Object to S3 with Metadata.</span>**

Create a `ObjectMetadata` object:

	ObjectMetadata myObjectMetadata = new ObjectMetadata();

	//create a map to store user metadata
	Map<String, String> userMetadata = new HashMap<String,String>();
	userMetadata.put(“myKey”,”myVal”);

	//call setUserMetadata on our ObjectMetadata object, passing it our map
	myObjectMetadata.setUserMetadata(userMetadata);

Then, upload the object with its metadata.

	TransferObserver observer = transferUtility.upload(
	  MY_BUCKET,        /* The bucket to upload to */
	  OBJECT_KEY,       /* The key for the uploaded object */
	  MY_FILE,          /* The file where the data to upload exists */
	  myObjectMetadata  /* The ObjectMetadata associated with the object*/
	);

**<span style="color:#007697">Download an object from S3</span>**

To download a file:

	TransferObserver observer = transferUtility.download(
	  MY_BUCKET,     /* The bucket to download from */
	  OBJECT_KEY,    /* The key for the object to download */
	  MY_FILE        /* The file to download the object to */
	);

**<span style="color:#007697">Tracking S3 Transfer Progress</span>**

With the Transfer Utility, the `download() ` and `upload()` method return the `TransferObserver` object.  This Object gives access to:

* The state (As Enum Type)
* The total bytes transferred thus far
* The total bytes to transfer
* A Unique id that you can use to keep track of distinct transfers

Using Track Id, the `TransferObserver` object can retrieved from anywhere in your application, even if the application is killed.  Let’s you create the `TransferListener`, which will update on state changed, progress change, as well as when an error occurs.

	TransferObserver transferObserver = download(MY_BUCKET, OBJECT_KEY, MY_FILE);
	transferObserver.setTransferListener(new TransferListener(){

	    @Override
	    public void onStateChanged(int id, TransferState state) {
	        // do something
	    }

	    @Override
	    public void onProgressChanged(int id, long bytesCurrent, long bytesTotal) {
	        int percentage = (int) (bytesCurrent/bytesTotal * 100);
	        //Display percentage transfered to user
	    }

	    @Override
	    public void onError(int id, Exception ex) {
	        // do something
	    }

	});

**<span style="color:#007697">Pause an S3 Transfer</span>**

If you application is killed, or loses the network connectivity, transfer are automatically paused. If you device running your app loses network connectivity, paused the transfer will automatically resume when the network is available. If the transfer manually paused or app was killed, the transfer can resume with the `resume(transferId)`.

To paused the single Transfer

	transferUtility.pause(idOfTransferToBePaused);

To paused all upload files

	transferUtility.pauseAllWithType(TransferType.UPLOAD);

To paused all downloads

	transferUtility.pauseAllWithType(TransferType.DOWNLOAD);

To pause all transfers of any type:

	transferUtility.pauseAllWithType(TransferType.ANY);


**<span style="color:#007697">Resume a Transfer</span>**

	transferUtility.resume(idOfTransferToBeResumed);


**<span style="color:#007697">Cancel a Transfer</span>**

	transferUtility.cancelAllWithType(TransferType.DOWNLOAD);
