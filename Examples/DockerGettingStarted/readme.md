The first step working with docker could be following the [Getting started tutorial](https://docs.docker.com/get-started/). 
- In the tutorial you will install Docker locally
- Then you can run a basic image
```
 docker run -d -p 80:80 docker/getting-started
```
Console output:
```
PS C:\source\_Personal\containers-labs\Examples\NetCoreWebApiDockerSupportLinux> docker run -d -p 80:80 docker/getting-started
Unable to find image 'docker/getting-started:latest' locally
latest: Pulling from docker/getting-started
df9b9388f04a: Pull complete
5867cba5fcbd: Pull complete
4b639e65cb3b: Pull complete
061ed9e2b976: Pull complete
bc19f3e8eeb1: Pull complete
4071be97c256: Pull complete
79b586f1a54b: Pull complete
0c9732f525d6: Pull complete
Digest: sha256:b558be874169471bd4e65bd6eac8c303b271a7ee8553ba47481b73b2bf597aae
Status: Downloaded newer image for docker/getting-started:latest
419df4a2853951c7901e3fb503de22cc80f1649a231801ac60e650d50fc9d074
```

- Then navigating to [localhost](http://localhost/) will show the running app