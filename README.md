# Meteor Buildpack

Tested with Dokku:
```
dokku config:set my-app BUILDPACK_URL=https://github.com/healthcareblocks/meteor-buildpack.git
```

## Hardcoded Build Flags
```
--architecture=os.linux.x86_64
--server-only
```

## Required Environment Variables

* MONGO_URL
* ROOT_URL

## Dokku-Related Notes

### Caching
The Dokku default of /cache (inside the container) is not leveraged by the various Meteor buildpacks floating around. This version will use Herokuish's default of /tmp/cache. To mount to an external host volume, you can do something like this:
```
mkdir -p /data/my-app-name/cache
chown -R www-data:www-data /data/my-app-name/cache
chmod -R 0777 /data/my-app-name/cache
dokku docker-options:add my-app-name build '-e USER=www-data -v /data/my-app-name/cache:/tmp/cache'
```

One advantage is that Meteor is only installed (to /tmp/cache/meteor) during the first deployment, shaving about 15-20 seconds from subsequent deploys.

### Prevent the Docker build container from misbehaving
```
dokku docker-options:add my-app-name build "--cpu-quota 75000"
```

This caps the container process at 75% CPU. And if you'd like the container to be able to use the host's swap memory without any limits, add ```--memory-swap -1``` to the above.

### Automatically load meteor-settings.json

Write them to /home/dokku/my-app/meteor-settings.json and then install this plugin:
```
dokku plugin:install https://github.com/JarnoLeConte/dokku-meteor.git
```

### Credits

Based on https://github.com/AdmitHub/meteor-buildpack-horse but with differences in buildpack detection and Dokku-biased optimizations noted above.
