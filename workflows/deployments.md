---
description: 'Best practices for using automated tooling to test, build and deploy projects.'
---

# Deployments

Most of our hosting partners offer Git repositories for deploying code to their infrastructure. 

However, the development related code and tooling shouldn't be uploaded to the production servers so we introduce **a build and deploy process** which:

* installs project dependencies,
* runs tests to confirm the functionality of the project, 
* builds the distribution versions of all assets such as JS and CSS,
* removes development-only source code, helpers and tooling,
* deploys the release bundle to the production environment.

In reality, it could look like the following sequence of scripts:

* `npm install`
* `npm run test`
* `npm run build`
* `npm run release`
* `npm run deploy`

All tooling to run the build and deploy process should be engineered in a way that is agnostic to the automation platform used for running the checks and deployments. This ensures that the whole process can be verified and tested locally, and ported to different automation systems without changing the core functionality. The main concern of the automation platform is to setup the platform-level dependencies such as the correct version of PHP, Node.js and Docker to ensure that the scripts bundled with the project can run.

## Access Management for Deployments

Automated build and deploy tooling needs access both to the source code and the deploy target repositories. In case of Git repositories the access is managed via SSH keys where the private part of the key is known only to the party accessing the code while the matching public key is added to the repositories.

### Unique SSH Keys

It is important that the SSH key pair is limited only to the repositories that are needed for the specific project and that the keys are not reused between projects. This ensures that a compromised key can't impact the workflows of other projects.

Use the following command to generate a new SSH key pair:

```text
ssh-keygen -m PEM -t rsa -b 4096 -C "technology+PROJECT_KEY@xwp.co"
```

where:

* `-m PEM` sets the output format PEM,
* `-t rsa` sets the key format to RSA,
* `-b 4096` sets the key length to 4096 bytes while
* `-C technology+PROJECT_KEY@xwp.co` is free-form comment to designate the project association.

Keys that are used for automation usually don't require a passphrase so that step can be skipped during the key generation. The path where to generate the keys can be anything as long as you're able to find them afterwards. Make sure _to not override_ any existing keys on your machine such as `~/.ssh/id_rsa`. Some automation systems support newer SSH key algorithms such as `Ed25519` in which case the `-m PEM` flag can be omitted.

The command generates two new files, if `key_name` was specified as the key name:

* `key_name` is the private part \(secret\) of the key which should be added to the automation platform \(Travis CI, GitHub Action, Circle CI\) that needs access to the protected resources -- source code and the deployment target repositories. See the setup instructions for [GitHub Actions](https://github.com/marketplace/actions/checkout#usage), [Travis CI](https://docs.travis-ci.com/user/private-dependencies/#using-an-existing-key) and [Circle CI](https://circleci.com/docs/2.0/add-ssh-key/).
* `key_name.pub` is the matching public key which should be added to all Git repositories that should be accesible by the computer possessing the private key such as the source and deployment repositories. See instructions for [VIP Go \(same as any GitHub repository\)](https://docs.github.com/en/developers/overview/managing-deploy-keys), [Pantheon](https://pantheon.io/docs/ssh-keys) and [WP Engine](https://wpengine.com/support/git/#Add_SSH_Key_to_User_Portal).

### Machine Users for Projects with Multiple Private Dependencies

For projects with multiple private dependencies it might be easier to use a dedicated GitHub user since GitHub deploy keys can't be re-used on multiple GitHub repositories. These are sometimes called "machine users" since they are only a convenience for the deployment as the user can be added to all relevant private repositories with the necessary permissions. For example, the public part of the SSH key is added to the [SSH keys of a machine user](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) `machine-user` on GitHub and then the `machine-user` is [invited to all the necessary repositories](https://docs.github.com/en/github/setting-up-and-managing-your-github-user-account/managing-access-to-your-personal-repositories/inviting-collaborators-to-a-personal-repository) so that any client authenticating with the key is now recognized as the `machine-user` with its access permissions.

## Source and Release Repositories

While our hosting partners provide Git repositories for tracking the production code, sometimes it is useful to keep a local source repository in our GitHub organization and use the host repository only as the release target.

Using a local source repository provides the following benefits:

* Add and remove project contributors without giving them access to the production environments.
* Track development files and limit what is deployed to the publicly accessible release repository.
* Use any automation workflow when developing, testing and deploying the project.

Both Pantheon and WP VIP offer ways to hide files from public access through either special "protected" directories --`/private` [on WP VIP](https://docs.wpvip.com/technical-references/vip-codebase/private-directory/) and [private paths](https://pantheon.io/docs/private-paths) or [dedicated `/web` sub-directory](https://pantheon.io/docs/nested-docroot) on Pantheon. It is unknown how to do this on WP Engine.

Note that VIP Go supports [using branches with `-built` suffix for tracking the release files](https://docs.wpvip.com/technical-references/development-workflow/automated-build-and-deploy/#h-pushing-code-to-branches) so the non-built branches can be used similar to a local source repository. This is a feature that must be enabled by VIP support after you've created the necessary branches -- `master-built` for production and `develop-built` for staging.

### Deploy to a Different Release Repository

Here is an illustration of the build and deployment process when a project is deployed to a release repository which is different from the source repository. A dedicated SSH key-pair is used to authenticate the automation tooling with both repositories for read and write access.

![CI automation pulling and building from a source repository and deploying to a dedicated release repository.](../.gitbook/assets/deploy-different-release-repository.svg)

### Deploy to a Branch in the Same Source Repository

The workflow is very similar when the same repository is used for both development and deployments:

![CI automation pulling and pushing to different branches in the same project repository.](../.gitbook/assets/deploy-same-site-repository.svg)

## Platform Specific Instructions

### WordPress VIP

See the [VIP Go site template](https://github.com/xwp/vip-go-site) for [WordPress VIP](https://wpvip.com/) specific instructions.

### WP Engine

We’ll be using the [official GitHub Action for WP Engine site deployments](https://github.com/marketplace/actions/deploy-wordpress-to-wp-engine). 

#### Step 1: Setup the GitHub Action for Deployments

1. In the root of your project repository create a folder `.github/workflows`.

2. Inside the folder create a file `main.yml` with the [suggested content](https://github.com/marketplace/actions/deploy-wordpress-to-wp-engine#simple-mainyml).

#### Step 2: Configure the GitHub Action

1. You’ll need to know your branches names and your environments names from WPEngine. Environments names can be found in "Sites" section in [WP Engine dashboard](https://my.wpengine.com/sites), they are marked like this:

    ![List of environments](../.gitbook/assets/wpengine-site-environments.png)

2. Now you only need to match them. For example, there are three branches and three environments each branch should be synced to:

    ```
    DEV_BRANCH: devbranch
    DEV_ENV: sitedev

    STG_BRANCH: stagebranch
    STG_ENV: sitestage

    PRD_BRANCH: prodbranch
    PRD_ENV: siteprod
    ```

#### Step 3: Give GitHub Action Deploy Access to WP Engine

1. Generate an SSH keys pair as described in the "Access Management for Deployments" section above.

2. Add the contents of the private key (the one, that doesn’t have `.pub` extension) as a repository secret `WPE_SSHG_KEY_PRIVATE`. Head to the GitHub repository of the project under "Settings > Secrets > New repository secret". If you don’t see this section, that might be because you don’t have the necessary permissions for the repository.

    ![GitHub repository settings for adding action secrets](https://user-images.githubusercontent.com/5646904/137309307-8981e7c7-7c85-4097-b132-95b1b4589d2c.png)

    The secret name should match the name in the `main.yml` configuration file! The default name is `WPE_SSHG_KEY_PRIVATE`:
  
    ![Add new Action secret to the repository](https://user-images.githubusercontent.com/5646904/137309366-8c2c678c-bc47-4141-a88c-303baddc3e3d.png)

3. Add the contents of the public key to your [WP Engine user profile](https://my.wpengine.com/ssh_keys) (instead of the ["Git push" section for each environment](https://wpengine.com/support/git/#Add_Existing_Git_User_to_Another_Environment) since the action [doesn't use the Git workflow](https://github.com/wpengine/github-action-wpe-site-deploy/blob/341ea21dfd5a340a4c808e9eafc9d2ebdb018904/entrypoint.sh#L43-L44)):

    ![SSH keys for repository](../.gitbook/assets/wpengine-user-profile-ssh-key-add.png)

Committing these changes to the repository will trigger a GitHub action which will push the code to the appropriate WPEngine environment. You can see the process real-time in GitHub repo under the "Actions" tab:

![List of GitHub action history](https://user-images.githubusercontent.com/5646904/137309670-77dd700a-65ac-497a-9332-0bce772aeb20.png)

There is a [video tutorial](https://wpengine-2.wistia.com/medias/crj1lp3qke) also.


 





