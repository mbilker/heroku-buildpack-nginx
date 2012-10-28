Crush + Lovely's Fork of essh's Nginx Buildpack
================================================

This fork defaults to a newer build of nginx, stored on our own S3 bucket.

Heroku buildpack: nginx
=======================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpack)
for nginx.

Usage
-----

Example usage:

    $ ls -R *
    conf:
    mime.types     nginx.conf.erb

    html:
    index.html

    $ heroku create --stack cedar --buildpack http://github.com/essh/heroku-buildpack-nginx.git
    ...

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack... done
    -----> Nginx app detected
    -----> Fetching nginx binaries
    -----> Vendoring nginx 1.0.11
    ...

The buildpack will detect your app as nginx if it has the file
`nginx.conf.erb` in the `conf` directory. You must define all `listen`
directives as `listen <%= ENV['PORT'] %>;` and also include `daemon off;` in
order for this buildpack to work correctly.

As an alternative to the above instructions you may wish to investigate
[heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi)
in order to support more complex use-cases such as compiling a static site
that is served by nginx or placing nginx in front of app server processes.

Hacking
-------

To modify this buildpack, fork it on Github. Push up changes to your fork, then
create a test app with `--buildpack <your-github-url>` and push to it.

To change the vendored binaries for nginx use the helper script in the
`support/` subdirectory. You may wish to edit the helper script to modify
the nginx build options to suit your needs. You'll need an S3-enabled
AWS account and a bucket to store your binaries in.

For example, you can change the vendored version of nginx to 1.2.4.

First, make sure you have a vulcan build server:

    $ vulcan create my-awesome-build-server

Then build your own Heroku-compatible version of nginx:

    $ export AWS_ID=XXX AWS_SECRET=XXX S3_BUCKET=XXX
    $ s3 create $S3_BUCKET
    $ support/package_nginx 1.2.4 8.31

The first argument to the package_nginx script is the nginx version. The
second argument is the version of PCRE to compile nginx against.

Open `bin/compile` in your editor, and change the following lines:

    NGINX_VERSION="1.2.4"
    S3_BUCKET=XXX

Commit and push the changes to your buildpack to your Github fork, then push
your sample app to Heroku to test. You should see:

    -----> Vendoring nginx 1.2.4
