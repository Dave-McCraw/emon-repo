emon-repo
=========

APT repository for OpenEnergyMonitor.org

### Update instructions

First you need to check out the upstream package repository (i.e. `emoncms/emoncms`) and export the source, using the tag associated with the new version (in this example, that tag is `8.0.9`):

    git archive 8.0.9 | gzip > ../emoncms-orig.tar.gz
    
Now check out the debian packaging repository (i.e. `Dave-McCraw/pkg-emoncms`) and import the source. This requires a local upstream branch and you'll need to filter out, then restore, the upstream readme in favour of the local one:

    git checkout upstream && git checkout master
    git import-orig --filter=readme.md --pristine-tar ../emoncms-orig.tar.gz && git checkout readme.md
    
You'll need to resolve any other conflicts as they arise.

Following this, you need to use `dch` to update the `debian/changelog` and `debian/NEWS` files. Make sure you have exported the necessary debian environment variables first (in your .bashrc):

    export DEBEMAIL="dave@mccraw.co.uk"
    export DEBFULLNAME="Dave McCraw"
    
Then (the second pair of commands updates from `UNRELEASED` to `unstable` release marker):

    dch -v 8.0.9
    dch -v 8.0.9 --news
    dch -r
    dch -r --news
    
Now make a commit, and tag the new debian version:

    git commit -a -m "debian changelogs"
    git tag -a -m "New debian version" debian/8.0.9
    git push origin master upstream pristine-tar debian/8.0.9

Make sure you push the tag, as well as the `master`, `upstream` and `pristine-tar` branches, up to GitHub, or it will hinder the next guy!

At last, you can build the `.deb` file (you need GPG set up to sign the files. Find any standard tutorial on that):

    git-buildpackage --git-pristine-tar
    
You'll now have a new `.deb` file in the parent directory. Check out the `Dave-McCraw/emon-repo` repository there, and then (from the parent directory) use `reprepro` to add the new version to the repo:

    reprepro -b emon-repo includedeb wheezy emoncms_8.0.9_armhf.deb
    
All being well, you can now commit your changes to the repo itself and get that pushed up to GitHub too:

    git add pool/unstable/e/emoncms/
    git commit -a -m "Add emoncms 8.0.9"
    git push origin master
    
Finally, submit pull requests on the `Dave-McCraw/emon-repo` and `Dave-McCraw/pkg-emoncms` repositories so I can get your changes and update the actual repo on Amazon S3...

Phew! :rocket:
