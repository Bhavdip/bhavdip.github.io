---
layout: post
title:	Introducing the Transfer Utility for the AWS SDK for Android Part1
image:	byword-icon.png
---

**<span style="color:#c60">AWS S3 TransferManager</span>**


Easiest way to transfer the data/object/file between mobile application and Amazon S3, S3 Transfer Utility for AWS SDK for Android.
{{ more }}

**<span style="color:#007697">What is difference between the Transfer Utility and the Transfer Manager?</span>**

Probably when we try to transfer file or object, need to manage all the complicate steps such like starting from managing multiple files, resume, pause and cancel and many more. The Transfer Utility is designed to manage all the mentioned steps and provide the simple and effective way to use.

The Transfer Utility is new client, with many of the same features as Transfer Manager. Some of the enhancements features are:

1.	It used the SQLite database to persist all the metadata of the transfers, allowing them to be resumed, paused without the developer having to serialize or persist of any data that previously they did using Transfer Manager.
2.	It used the android service so you do not need to worry about creating thread, app crashed and killed. 
3.	It automatically managed the resumes and pauses when the internet connectivity is lost and gained. 
4.	It maintains the order of the uploading/downloading queue.
5.	It has general configuration improvements for performance with S3 on mobile.


**<span style="color:#007697">Uploading and downloading</span>**

The Transfer Manager has APIs that support the use of I/O streams, with these APIs, pausing, Resuming was not possible. With using Transfer Utility Manager there is one file based API that support the resume and pause on top of automatic pause or resume mentioned above. The Transfer Utility majorly provided two operations download & upload.
	
	TransferObserver observer = transferUtility.upload(
	MY_BUCKET,           // The S3 bucket to upload to (String bucket Name)
	OBJECT_KEY,          // The key for the uploaded object  (String)
	FILE_TO_UPLOAD       // The location of the file to be uploaded (File)
	);

`For example:`

	File file = new File(filePath);
	TransferObserver observer = transferUtility.upload(Constants.BUCKET_NAME, file.getName(),file);


**<span style="color:#007697">Downloading is equally simple:</span>**
	
	TransferObserver observer = transferUtility.download(
	MY_BUCKET,           /* The bucket to download from */
	OBJECT_KEY,          // The key for the object to download
	FILE_TO_DOWNLOAD     // The file for download the object
	); 
	

**<span style="color:#007697">Tracking Transfer Progress:</span>**

The Transfer Utility makes tracking the transfer make mobile friendly. The basic way to track the transfers is thorough instances of the `TransferObserver` class. `TransferObserver` instance are returned from `download()` and `upload()` methods. They automatically persist into local database. We can queried for base on `id`,`type (upload, download, or any)` or `state (Such as paused)`from anywhere within the app see blow.

	
	public TransferObserver getTransferById(int id)

	public List<TransferObserver> getTransfersWithType(TransferType type) 

	public List<TransferObserver> getTransfersWithTypeAndState(TransferType type, TransferState state)

	

The TransferObserver gives access to the state, total bytes transferred thus far, total bytes to transfer and a unique id you can use to keep distinct transfers. You can also attached the listener `TransferListener`,which will update `onStateChanged`, `onProgressChanged` and an `onError` occurs.

	transferObserver.setTransferListener(new TransferListener() {
	 
	@Override
	public void onStateChanged(int id, TransferState state){
	//Do something on state change
	}
	 
	@Override
	public void onProgressChanged(int id, long bytesCurrent, long bytesTotal) {
	//Do something on progress change.
	}
	 
	@Override
	public void onError(int id, Exception ex) {
	//Do something on error
	}
	});


**<span style="color:#007697">Pausing Transfers:</span>**

With Transfer Manager, there is no guarantee a transfer can paused and there are multiple way to paused. Also paused require developer to serialize the metadata of the transfer to persist to storage. 

The Transfer Utility handlers persists all the metadata of the transfer for you. Even if you application killed, crashed or loss connectivity transfers automatically paused. Even you can manually paused the transfer by id with `pause(transferd id)`paused all downloads
`pauseAllWithType(TransferType.Download)` and pause all transferred by `pauseAllWithType(TransferType.ANY)`.

**<span style="color:#007697">Resuming Transfers:</span>**

The Transfer Utility can paused the transfer in many scenarios. In the case of transfer was paused due to of the loss connectivity, the Transfer will automatically Resume when the network connection available again. In case of manually paused the transfer, application killed, crashed, it can be resumed with `resume(TransferId)` method.

In summary, Transfer Utility is completely replaced with the Transfer Manager. Going forward AWS will be deprecated the Transfer Manager.

**Additional reference related to this topic you can find in blow:**

<a href="https://github.com/awslabs/aws-sdk-android-samples/blob/master/S3TransferUtilitySample/src/com/amazonaws/demo/s3transferutility/UploadActivity.java" target="_blank">GitHub Sample</a> 

<a href="http://mobile.awsblog.com/post/Tx16PLJMWD1K9ZX/Introducing-the-Transfer-Utility-for-the-AWS-SDK-for-Android" target="_blank">Offical blog</a>