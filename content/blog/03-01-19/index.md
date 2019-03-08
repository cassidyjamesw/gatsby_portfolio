---
title: Labs Week Three
date: "2019-03-01T22:12:03.284Z"
---

### Week Three Accomplishments:

- AWS S3 Integration
- SendGrid

## AWS S3

[Link to our S3 Paid Routes](https://github.com/Lambda-School-Labs/labs10-big-files/blob/master/lambdafiles/routes/api/s3.js "S3 Routes")

[S3 PR1](https://github.com/Lambda-School-Labs/labs10-big-files/pull/97)

[S3 PR2](https://github.com/Lambda-School-Labs/labs10-big-files/pull/102)

[S3 PR3](https://github.com/Lambda-School-Labs/labs10-big-files/pull/103)

We used S3 to upload and download files. S3 makes this process relatively simple, but as a file hosting site - this is a major piece of our project. We used a combination of the [AWS-SDK](https://aws.amazon.com/sdk-for-node-js/), [Multer](https://www.npmjs.com/package/multer), and [MulterS3](https://www.npmjs.com/package/multer-s3) to accomplish this. The AWS-SDK allows us to use node to communicate with our S3 buckets easily. Multer is a popular package for uploading files, and MulterS3 is specifically designed to upload files to S3.

This week we specifically seperated out routes for paid users, so they could upload more data. Currently we are working on lifecycle methods so that after a period of time we can remove the data users upload.

This is our code for uploading files into S3 for paid users:

    const paidFileUpload = multer({
    storage: multerS3({
        s3: s3,
        bucket: "s3lambdafiles123",
        // acl: "public-read",
        key: function(req, file, cb) {
        cb(
            null,
            path.basename(file.originalname, path.extname(file.originalname)) +
            "-" +
            Date.now() +
            path.extname(file.originalname)
        );
        }
    }),

    limits: { fileSize: 4000000 } // In bytes: 4000000 bytes = 4 MB
    }).single("fileUpload");

## SendGrid

[Link to our SendGrid Routes](https://github.com/Lambda-School-Labs/labs10-big-files/blob/master/lambdafiles/routes/api/sendgrid.js)

[Sendgrid PR](https://github.com/Lambda-School-Labs/labs10-big-files/pull/98)

[Sendgrid PR2](https://github.com/Lambda-School-Labs/labs10-big-files/pull/111)

[Sendgrid PR3](https://github.com/Lambda-School-Labs/labs10-big-files/pull/112)

[SendGrid](https://sendgrid.com/) is a very popular email delivery service. After being denied to send emails without prior verification from AWS SES, we choose to work with sendgrid and are quite pleased with the results. Sendgrid has an [NPM Package](https://www.npmjs.com/package/@sendgrid/mail) that allows us to easily interact with the mail endpoint of the (Sendgrid v3 API)[https://sendgrid.com/docs/API_Reference/api_v3.html].

The following is an example of using SendGrid v3 API to send emails.

    const sgMail = require('@sendgrid/mail');
    sgMail.setApiKey(process.env.SENDGRID_API_KEY);

    router.post("/send", (req, res) => {
    const {to, from, subject, text, html} = req.body;
    const msg = {
        to: to,
        from: from,
        subject: subject,
        text: text,
        html: html
    };
    sgMail.send(msg);
    console.log("email sent");
    res.send("Email Sent");
