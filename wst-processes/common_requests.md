---
title: Common Requests
parent: WST Processes
layout: default
---

# Delete someone's account

WRT handles these requests - forward any such requests to them via email.

{: .info}
> The WCA has processes for anonymizing Results data -- i.e. "soft deleting" a WCA ID. We do not however comply with any requests to delete accounts in the stricter sense, i.e. username/password combinations for people to log in with

# Remove an avatar

1. Delete the avatar from the S3 bucket
    1. Bucket can be accessed with this link: https://s3.console.aws.amazon.com/s3/buckets/wca-avatar?region=us-west-2&prefix=uploads/user/avatar/&showversions=false
        1. Alternatively: Naviate to AWS Console -> S3 -> Buckets -> wca-avatar -> uploads -> user -> avatar
    2. Navigate to the user's WCA ID 
    3. Select all items you want to delete (delete the avatar in question and the thumbnail for it)
    4. Type the required text in the confirmation page
2. Invalidate the avatar from the avatars Cloudfront distribution
    1. Search "Cloudfront" in AWS 
    2. Select the distribution with "avatars" in the "Alternate destination" and/or "Origins" columns
    3. Go to "Invalidations" -> "Create Invalidation"
    4. Create an invalidation with the URL where the avatar lives e.g /uploads/user/avatar/2019POLA01/1569217830.jpg
