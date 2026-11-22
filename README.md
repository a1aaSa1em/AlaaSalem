# Alaa Salem — portfolio

A responsive, dependency-free personal portfolio. The site links to selected public GitHub projects and includes an AWS CloudFormation template for a private S3 origin served over HTTPS by CloudFront. The AWS deployment is prepared; it has not yet been deployed or verified.

## Preview locally

Open `site/index.html` in a browser, or run `python3 -m http.server 8000 --directory site` and visit http://localhost:8000.

## Deploy on AWS

You need an AWS account, permission to create CloudFormation, S3 and CloudFront resources, and AWS CLI v2 configured using your preferred secure sign-in method. AWS resources can incur charges; check current pricing and your account's free-tier eligibility first. No custom domain is required.

1. From the project directory, create the infrastructure:

   ```sh
   aws cloudformation deploy --template-file cloudformation.yaml --stack-name alaa-portfolio --region eu-west-2
   ```

2. In CloudFormation, open the stack's **Outputs** and note `BucketName`, `DistributionId`, and `WebsiteURL`.
3. Upload the site (replace the placeholder):

   ```sh
   aws s3 sync site/ s3://YOUR-BUCKET-NAME/
   ```

4. Open `WebsiteURL` once CloudFront finishes deploying. For subsequent updates, sync again and invalidate cached files:

   ```sh
   aws cloudfront create-invalidation --distribution-id YOUR-DISTRIBUTION-ID --paths '/*'
   ```

5. Check the public URL on desktop and mobile. Never commit AWS credentials to the repository.

The bucket blocks public access; CloudFront signs requests to the bucket using origin access control. Only the website content should be uploaded. The CloudFront URL is public after deployment.

To remove resources, empty the S3 bucket first, then delete the CloudFormation stack. CloudFront removal can take time. See [AWS's secure static website guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/getting-started-secure-static-website-cloudformation-template.html) and [S3 origin access control documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html).
