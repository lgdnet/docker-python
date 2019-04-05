# GPU build of Kaggle's python docker image

- This project https://github.com/lgdnet/docker-python is a fork of Kaggle's [docker-python](https://github.com/Kaggle/docker-python) docker image

- Reason : so far [Kaggle](www.kaggle.com) does not publish any GPU version of this image
- In order to maintain an automated build on [dockerdub](hub.docker.com) we need to:
  - keep our fork up to date
  - configure automated builds on [dockerdub](hub.docker.com)

## Keep our fork up to date

- this is somewhat intricated, see [this Stackoverflow entry](https://stackoverflow.com/questions/7244321/how-do-i-update-a-github-forked-repository/31836086#31836086) and [this Github help page](https://help.github.com/en/articles/syncing-a-fork).
  Prefer the rebase method, as the merge method will create an extra commit ahead on your project.

```bash
#####################################################
# Only once a a local machine
#####################################################
git clone git@github.com:lgdnet/docker-python.git
cd docker-python/

# add Kaggle's remote, call it "kaggle-docker-python"
git remote add kaggle-docker-python https://github.com/Kaggle/docker-python.git

# See where we stand
git remote -v
git remote show origin
git remote  show kaggle-docker-python

#####################################################
# synching our fork
#####################################################
# Make sure
git checkout master

# Rewrite your master branch so that any commits of yours that aren't already in upstream/master are replayed on top of that other branch:
git fetch kaggle-docker-python master
git rebase kaggle-docker-python/master

# If you don't want to rewrite the history of your master branch, (for example because other people may have cloned it) then you should replace the last command with git merge upstream/master. However, for making further pull requests that are as clean as possible, it's probably better to rebase.

git push -f origin master
# You only need to use the -f the first time after you've rebased.
```

:warning: never commit to master except this file !

## Configuring automated build

- On [dockerhub organisation `lgdnet`](https://cloud.docker.com/u/lgdnet/settings)'s follow [settings/Linked Accounts]()

- On project [lgdnet/docker-python](https://cloud.docker.com/u/lgdnet/repository/docker/lgdnet/kaggle-python) on [dockerhub](https://hub.docker.com/?namespace=lgdnet) ensure GithHub's `lgdlab` is mentionned.

```text
Linked Accounts
These account links are used for Automated Builds, so that Docker Hub can access your project lists and help you configure your Automated Builds. Please note: A Github/Bitbucket account can only be connected to one Docker Hub account at a time.

Service user (or machine/bot account) suggested
Attaching your personal GitHub or Bitbucket account to this Docker Hub organization will allow other organization owners to create builds from your private repositories. We suggest using a service user (also referred to as a machine user or bot account).
```

- If the link does not exist, follow this [Docker help page](https://docs.docker.com/docker-hub/builds/link-source/).
  - Click Settings in the top-right dropdown navigation.
  - Click or scroll down to Linked Accounts.
  - Click the plug icon for the source provider you want to link.
- link to Github's organisation `lgdnet`, account `lgdnet-service-account`, which is (should be) a member of team `build` with admin rights on Github project [lgdnet/docker-python](https://github.com/lgdnet/docker-python)

- follow [Builds/Configure Automated Builds](https://cloud.docker.com/u/lgdnet/repository/docker/lgdnet/kaggle-python/builds/edit) and specify configurations:

- | key        | value                 |
  | ---------- | --------------------- |
  | source rep | lgdnet, docker-python |
  | autotest   | off                   |
  | rep links  | enable for base image |

- build rules:

  | Source Type | Source | Docker Tag | Dockerfile location | Build Context | Autobuild | Build Caching |
  | ----------- | ------ | ---------- | ------------------- | ------------- | --------- | ------------- |
  | branch      | master | gpu-latest | gpu.Dockerfile      | /             | no ?      | yes ?         |
