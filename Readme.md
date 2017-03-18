# tinymce-aws-s3-upload-plugin

This plugin adds a button to your TinyMCE editor which uploads selected file to your Amazon S3 bucket.

[🦄 Working Demo](http://ogpoyraz.com/tinymce-aws-s3-upload-plugin)

![tinymce-aws-s3-upload-plugin](http://ogpoyraz.com/tinymce-amazon-s3-upload-plugin/tinymce-amazon-s3-upload-demo.gif)

## Usage

### 1. Add plugin javascript 
Add ```dist/tinymce-aws-s3-upload-plugin.min.js``` in to your html.
 
Or
 
```bash
npm i tinymce-aws-s3-upload-plugin
```
```javascript
import 'tinymce-aws-s3-upload-plugin'
```

### 2. Add AWS SDK javascript 
-You can skip this part if you done with the authentication to AWS.

Add ```https://sdk.amazonaws.com/js/aws-sdk-2.5.3.min.js``` in to your html.

#### More information about Configuring AWS SDK in the browser
AWS doesn’t recommend to use them on front-end code(js) because if somebody take a look and get these keyId and secretKey they can reach your AWS resources.[Here you can find more information.](http://docs.aws.amazon.com/AWSJavaScriptSDK/guide/browser-configuring.html)

#### Secure way to do add accessKeyId & secretAccessKey 

##### 2.1 Create a new IAM user

![createAnIamUser](http://ogpoyraz.com/tinymce-amazon-s3-upload-plugin/createAnIamUser.png)

##### 2.2 Create a new policy which is only have access policy for S3 putObject

![createNewPolicy](http://ogpoyraz.com/tinymce-amazon-s3-upload-plugin/createNewPolicy.png)
![allowOnlyPutObjectPolicy](http://ogpoyraz.com/tinymce-amazon-s3-upload-plugin/allowOnlyPutObjectPolicy.png)

##### 2.3 Select the policy for new User

![refreshThePolicyTab](http://ogpoyraz.com/tinymce-amazon-s3-upload-plugin/refreshThePolicyTab.png)

```javascript
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:PutObject"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::*"
            ]
        }
    ]
}

```
![selectTheNewPolicyYouAdded](http://ogpoyraz.com/tinymce-amazon-s3-upload-plugin/selectTheNewPolicyYouAdded.png)



##### 2.4 Note  accessKeyId & secretAccessKey for this user. Now you can safely use these keys

![youHaveIAMUserWhichAccessS3Only](http://ogpoyraz.com/tinymce-amazon-s3-upload-plugin/youHaveIAMUserWhichAccessS3Only.png)


 
### 3. Configure Plugin and Init TinyMCE
```javascript
tinymce.init({
            selector: 'textarea',
            height: 500,
            menubar: false,

            // Plugin configuration
            plugins: 'AwsS3Upload',
            toolbar: 'AwsS3UploadButton',
            Awss3UploadSettings: {
                buttonText: 'Upload to AWS S3',  // optional
                folderName: 'YOUR_FOLDERNAME',   // optional
                bucketName: 'YOUR_BUCKETNAME',
                awsAuth: {                       // optional if auth done in html before
                    region: 'YOUR_BUCKET_REGION',// for example : 'us-east-1'
                    accessKeyId: 'YOUR_ACCESSKEYID',
                    secretAccessKey: 'YOUR_SECRETACCESSKEY'
                },
                progress: {
                    bar:true,                    // optional default=true
                    callback: progress => {      // optional
                        console.log(progress)
                    },
                    errorCallback: err => {      // optional
                        console.log(err)
                    },
                    successCallback:(editor,url) => {  // optional
                        // For example
                        switch(url.split('.').pop()){
                            case 'png':
                            case 'jpg':
                            case 'jpeg':{
                                editor.execCommand('mceInsertContent', false, `<img src="${url}" style="display: block;margin: 0 auto;text-align: center; max-width:100%;" />`);
                                break;
                            }
                            default:{
                                editor.execCommand('mceInsertContent', false, `<a href="${url}">${url}</a>`);
                            }
                        }
                    }
                },
                secondFileSelectedBeforeFirstUpload:{ // optional
                    callback:()=>{
                        alert('You cannot upload because first upload is progressing');
                    }
                }
            }
        });
```
## Development

Clone repo & navigate into it

```bash
npm i
npm run development
```

& browse http://localhost:3000

Thanks for reading.

Thanks for reading.


