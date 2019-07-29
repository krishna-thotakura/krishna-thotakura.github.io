
## Accessing Oracle from AWS Lambda NodeJS application

#### Pre-reqs
* Node 10.16.x 
* NPM oracledb package
* Download oracle Instant Client light linux version zip file from https://www.oracle.com/database/technologies/instant-client/downloads.html to /tmp

### Creating your Lamdba deployment zip file

Let us say you are developing your lambda code in the directory lambda-app and you are going to create a dist folder containing all the files that will be deployed to lambda.
```
mkdir dist
cp index.js dist/
cp -R node_modules/oracledb/** dist/

mkdir -p dist/lib
unzip /tmp/instantclient-basiclite-linux.x64-19.3.0.0.0dbru.zip -d dist/lib/

# The symlinks in dist/lib folder should be intact and not dereferenced.
cp -H /usr/lib64/libaio* dist/lib/

# Ensure rw permissions for lambda.
find . -type d -print0 |xargs -0 chmod 755
find . -type f -print0 |xargs -0 chmod 644
chmod 755 dist/lib/*.so

# Now zip up your entire dist folder
cd dist
zip ../xyz.zip --symlinks -r .

# Verify the zip file
zipinfo xyz.zip
````
Your zip file should look similar to below.
 ![Zip deployment package for connecting to Oracle from Lambda](../images/external-libraries-in-lambda.PNG)

Upload the zip file to s3 and have your lambda use the zip deployment package from s3.
