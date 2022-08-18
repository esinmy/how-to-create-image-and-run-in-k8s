# How to create Docker image and deploy it in K8s using Helm #

For educational reasons, we are creating a simple Docker image that has Nginx server with our custom index.html. This part of project is located in *docker-image-creation* folder.

Run the following command inside the *docker-image-creation* folder to create a Docker image:
```
docker build -t web-server:v1 . 
```

Helm chart blueprint can be created by running the following:
```
helm create <chart_name>
```

Only a few files have been kept and modified in the *helm-chart-creation* folder in order to have a minimally working example.
Create a package that can be placed in the Helm repository (run inside the *helm-chart-creation* folder):
```
helm package .
```

Archive `my-webserver-0.1.0.tgz` will be created in the *helm-chart-creation* folder.

To publish this chart to GitLab Helm package registry:
```
curl --request POST \
     --form 'chart=@my-webserver-0.1.0.tgz' \
     --user <username>:<access_token> \
     <CI_API_V4_URL>/projects/<project_id>/packages/helm/api/<channel>/charts
```

* `<username>`: the GitLab username or the deploy token username.
* `<access_token>`: the personal access token or the deploy token.
* `<project_id>`: the project ID (like 42) or the URL-encoded path of the project (like group%2Fproject).
* `<channel>`: the name of the channel (like stable).

To install a remotely located Helm chart, first, you need to add this Gitlab Helm package registry:
```
helm repo add --username <username> --password <access_token> <your_picked_repo_name> <CI_API_V4_URL>/projects/<project_id>/packages/helm/<channel>
```

Then install the cart as usual:
```
helm install <release_name> <your_picked_repo_name>/my-webserver
``` 