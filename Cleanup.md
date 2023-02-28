## Just to aid you cleanup properly
1. Navigate to route53 and delete the cloudfront distribution A record You can leave the hosted zone and ssl certificate in ACM if you wish its free.
2. Navigate to cloudfront and disable the distribution, wait for the `deploying` state under status to complete before you can delete the distribution. while waiting,
3. navigate to s3, select your bucket, empty bucket and further delete bucket. You can leave the empty bucket if you wish. (doesnt inquire charges)
4. Head back to cloudfront and delete the distribution.



