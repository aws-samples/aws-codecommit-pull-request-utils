Lambda function that both kicks off a CodeBuild build for a new/updated CodeCommit pull request, and comments on the pull request with the results of the build.

# Instructions

The default AWS JS SDK installed in the Lambda environment does not yet have the CodeCommit pull request APIs, so this function requires bundling the SDK into the function zip.
```
npm install
zip -r lambda.zip *
```

Upload the zip to a Lambda function, with `index.handler` for the handler and a Node.js runtime.

Add a CloudWatch Events trigger for the function, using the rule pattern in `cloudwatch_events_rule_pattern.json`.

# Policy

The Lambda function's role will need permissions to CodeCommit, CodeBuild, and CloudWatch Logs:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PullRequestBot",
            "Effect": "Allow",
            "Action": [
                "codebuild:StartBuild",
                "codecommit:PostCommentForPullRequest",
                "logs:GetLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
```

# Naming

This function assumes that the relevant CodeBuild project for pull request builds has the exact same name as the CodeCommit repository.  Update the logic if you have a different mapping scheme between CodeCommit repos and CodeBuild projects.