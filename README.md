JupyterHub docker image for compmodels
=====================

This is a configuration of JupyterHub that runs inside a docker container, uses
GitHub OAuth for authentication, and spawns additional docker containers with
user notebook servers.

## Building

Run the following command to build the base `jhamrick/jupyterhub` image:

    docker build -t jhamrick/jupyterhub .

Or, just pull it from docker hub with:

    docker pull jhamrick/jupyterhub

You will need to build a special deployment version of the image on top of
`jhamrick/jupyterhub`. In a different directory, create a `Dockerfile` with the
following contents:

    FROM jhamrick/jupyterhub

Additionally, create a `userlist` file that has usernames, user ids, and admin
priviliges, like so:

    jhamrick:1002 admin
    aphacker:1003
    bbitdiddle:1004

Then, build the deployment image:

    docker build -t jhamrick/jupyterhub:deploy .

## Running

To run JupyterHub, you'll first need to get the `jupyter/systemuser` image:

    docker pull jupyter/systemuser

Create a file called `env` that contains environment variables called
`$GITHUB_CLIENT_ID`, `$GITHUB_CLIENT_SECRET`, and `$OAUTH_CALLBACK_URL` for the
GitHub OAuth application. If the application has not yet been created, it can be
created [here](https://github.com/settings/applications/new). Make sure the
callback URL is:

    http[s]://[your-host]/hub/oauth_callback

Where `[your-host]` is where your server will be running. Such as
`example.com:8000`.

Finally, run JupyterHub with:

    docker run -d --env-file=env --net=host --name jupyterhub -v /var/run/docker.sock:/docker.sock jhamrick/jupyterhub:deploy
