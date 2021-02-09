# Blogging With Jekyll

This is my Jekyll build for blogging on [blog.gregdelima.com](http://blog.gregdelima.com). As a former WordPress user I wanted something a bit more "self-contained" without having to deal with a separate database.

The build is used with `docker-compose` from the `latest` jekyll image. 

```yaml
version: '3'

services:
  jekyll:
    container_name: jekyll
    image: jekyll/jekyll:latest
    command: jekyll serve --watch --force_polling --verbose
    ports:
      - 4000:4000
    volumes:
      - .:/srv/jekyll
```

I'm using the _jekyll-uno_ theme forked from [matthiaslischka](https://github.com/matthiaslischka/jekyll-uno). I found his fork to need a bit fewer tweaks than the original from [joshgerdes](https://github.com/joshgerdes/jekyll-uno).

In order to use the theme, I forked the repo and used [Jekyll Remote Theme](https://github.com/benbalter/jekyll-remote-theme) to bring it in. To bring it in edit the [_config.yml](_config.yml) file to include your remote theme following the guide.