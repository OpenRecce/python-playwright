# Building

How do I create a new playwright ubuntu image?

- `gh repo clone microsoft/playwright-python`
- `cd playwright-python/utils/docker`
- `./build.sh --amd64 <focal:jammy> <your-tag>`

Note: this will install all browsers. If you only need `webkit` for instance,
you'll need to edit `Dockerfile.<ubuntu-flavor>` and where it says
`playwright install --with-deps` add in the browser you want. Example,
`playwright install webkit --with-deps`. This will reduce the image size by 500mb
in this case.

The dev's have done an average job and making this docker image small so building
this locally and pushing it up to a registry is an effective way to reduce the bloated image.

Now push it up like so.

```bash
docker tag <your-tag> <registry>/<username>/<tag-name:-playwright-v1.30.0-webkit-focal>
docker push <registry>/<username>/<tag-name:-playwright-v1.30.0-webkit-focal>
```
