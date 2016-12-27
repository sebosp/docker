# React kubernetes bootcamps
## Background
This is a learning path for react and kubernetes.
At no point should this be taken other than for understanding parts the process.
I'm following the hello-world/Getting-Started guides:
- [React hello world](https://facebook.github.io/react/docs/hello-world.html)
- [Kubernetes Getting Started](http://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [Webpack Getting Started](https://webpack.github.io/docs/tutorials/getting-started/)

## Configuration
.babelrc enables `babel-preset-react` and `babel-preset-es2015`

## Requirements
- Yarn package manager downloaded [as documented here](https://yarnpkg.com/en/docs/install#alternatives-tab)
```shell
curl -L https://yarnpkg.com/latest.tar.gz -o yarn-latest.tar.gz
```

## Caveats
- "webuser" UID 1000 created for volume shares matching host desktop user UID.
  This hardcoding should be solved.
- root installs the following hierarchy, gives read permissions to all then.
  - `/usr/src/app/node_modules`
  - `/home/webuser/.npm-global`
- `/usr/src/app/node_modules/.cache` is left for the webuser for further operations.

## Running it without kubernetes
```shell
[/data/git/docker/kubernetes/react]$ docker build -t nodereact:0.0.1 .
[/data/git/docker/kubernetes/react]$ docker run -p 3000 -v $PWD/app/src/:/usr/src/app/src -v $PWD/app/public/:/usr/src/app/public --name react -d nodereact:0.0.1
```

## Running on kubernetes
Dockerfile was changed slightly to avoid mounting and having all resources ready:
```shell
tar -czf app.tar.gz app
```
After tagging, logging and pushing to the Docker Hub:
```shell
[/]# kubectl.sh run test1 --image=docker.io/sebosp/kubenodereact:0.0.1 --port=3000
[/]# kubectl.sh get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'
test1-4000106194-j38jm
[/]# kubectl.sh proxy
Starting to serve on 127.0.0.1:8001

Open in a browser: 
http://localhost:8001/api/v1/proxy/namespaces/default/pods/<PODNAME>/
Following the output from the example above:
http://localhost:8001/api/v1/proxy/namespaces/default/pods/test1-4000106194-j38jm/
```

### Issues
The logo URL is not provided correctly:
`http://localhost:8001/static/media/logo.5d5d9eef.svg` (missing api/v1/proxy/...)
Might be a problem with tho kube proxy setup or lack of config on the React Component.

## TODO
- `BABEL_ENV` added to docker run when stable with value `production`
- Use `setfacl` to grant read access to webuser? Or is this excessive?
