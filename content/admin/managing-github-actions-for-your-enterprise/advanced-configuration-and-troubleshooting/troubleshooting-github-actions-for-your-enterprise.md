---
title: Troubleshooting GitHub Actions for your enterprise
intro: 'Troubleshooting common issues that occur when using {% data variables.product.prodname_actions %} on {% data variables.product.prodname_ghe_server %}.'
permissions: 'Site administrators can troubleshoot {% data variables.product.prodname_actions %} issues and modify {% data variables.product.prodname_ghe_server %} configurations.'
versions:
  ghes: '*'
type: how_to
topics:
  - Actions
  - Enterprise
  - Troubleshooting
redirect_from:
  - /admin/github-actions/troubleshooting-github-actions-for-your-enterprise
  - /admin/github-actions/advanced-configuration-and-troubleshooting/troubleshooting-github-actions-for-your-enterprise
shortTitle: Troubleshoot GitHub Actions
---

## Checking the health of {% data variables.product.prodname_actions %}

You can check the health of {% data variables.product.prodname_actions %} on {% data variables.location.product_location %} with the `ghe-actions-check` command-line utility. For more information, see [AUTOTITLE](/admin/configuration/configuring-your-enterprise/command-line-utilities#ghe-actions-check) and [AUTOTITLE](/admin/configuration/configuring-your-enterprise/accessing-the-administrative-shell-ssh).

## Configuring self-hosted runners when using a self-signed certificate for {% data variables.product.prodname_ghe_server %}

{% data reusables.actions.enterprise-self-signed-cert %} For more information, see [AUTOTITLE](/admin/configuration/configuring-network-settings/configuring-tls).

### Installing the certificate on the runner machine

For a self-hosted runner to connect to a {% data variables.product.prodname_ghe_server %} using a self-signed certificate, you must install the certificate on the runner machine so that the connection is security hardened.

For the steps required to install a certificate, refer to the documentation for your runner's operating system.

### Configuring Node.JS to use the certificate

Most actions are written in JavaScript and run using Node.js, which does not use the operating system certificate store. For the self-hosted runner application to use the certificate, you must set the `NODE_EXTRA_CA_CERTS` environment variable on the runner machine.

You can set the environment variable as a system environment variable, or declare it in a file called `.env` in the self-hosted runner application directory (that is, the directory into which you downloaded and unpacked the runner software).

For example:

```shell
NODE_EXTRA_CA_CERTS=/usr/share/ca-certificates/extra/mycertfile.crt
```

Environment variables are read when the self-hosted runner application starts, so you must set the environment variable before configuring or starting the self-hosted runner application. If your certificate configuration changes, you must restart the self-hosted runner application.

### Configuring Docker containers to use the certificate

If you use Docker container actions or service containers in your workflows, you might also need to install the certificate in your Docker image in addition to setting the above environment variable.

## Configuring HTTP proxy settings for {% data variables.product.prodname_actions %}

{% data reusables.actions.enterprise-http-proxy %}

If these settings aren't correctly configured, you might receive errors like `Resource unexpectedly moved to https://IP-ADDRESS` when setting or changing your {% data variables.product.prodname_actions %} configuration.

## Runners not connecting to {% data variables.product.prodname_ghe_server %} with a new hostname

{% data reusables.enterprise_installation.changing-hostname-not-supported %}

If you deploy {% data variables.product.prodname_ghe_server %} in your environment with a new hostname and the old hostname no longer resolves to your instance, self-hosted runners will be unable to connect to the old hostname, and will not execute any jobs.

You will need to update the configuration of your self-hosted runners to use the new hostname for {% data variables.location.product_location %}. Each self-hosted runner will require one of the following procedures:

* In the self-hosted runner application directory, edit the `.runner` and `.credentials` files to replace all mentions of the old hostname with the new hostname, then restart the self-hosted runner application.
* Remove the runner from {% data variables.product.prodname_ghe_server %} using the UI, and re-add it. For more information, see [AUTOTITLE](/actions/hosting-your-own-runners/managing-self-hosted-runners/removing-self-hosted-runners) and [AUTOTITLE](/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners).

## Troubleshooting failures when {% data variables.product.prodname_dependabot %} triggers existing workflows

{% data reusables.dependabot.dependabot-on-actions-troubleshooting-workflows %} For more information, see [Providing workflows triggered by {% data variables.product.prodname_dependabot %} access to secrets and increased permissions](#providing-workflows-triggered-by-dependabot-access-to-secrets-and-increased-permissions) below.

### Providing workflows triggered by {% data variables.product.prodname_dependabot %} access to secrets and increased permissions

1. Log in to the administrative shell using SSH. For more information, see [AUTOTITLE](/admin/configuration/configuring-your-enterprise/accessing-the-administrative-shell-ssh).
1. To remove the limitations on workflows triggered by {% data variables.product.prodname_dependabot %} on {% data variables.location.product_location %}, use the following command.

    ``` shell
    ghe-config app.actions.disable-dependabot-enforcement true
    ```

1. Apply the configuration.

    ```shell
    ghe-config-apply
    ```

1. Return to {% data variables.product.prodname_ghe_server %}.

<a name="bundled-actions"></a>

## Troubleshooting bundled actions in {% data variables.product.prodname_actions %}

If you receive the following error when installing {% data variables.product.prodname_actions %} in {% data variables.product.prodname_ghe_server %}, you can resolve the problem by installing the official bundled actions and workflow templates.

```shell
A part of the Actions setup had problems and needs an administrator to resolve.
```

To install the official bundled actions and workflow templates within a designated organization in {% data variables.product.prodname_ghe_server %}, follow this procedure.

1. Identify an organization that will store the official bundled actions and workflow templates. You can create a new organization or reuse an existing one.
    * To create a new organization, see [AUTOTITLE](/organizations/collaborating-with-groups-in-organizations/creating-a-new-organization-from-scratch).
    * For assistance with choosing a name for this organization, see [AUTOTITLE](/admin/github-actions/getting-started-with-github-actions-for-your-enterprise/getting-started-with-github-actions-for-github-enterprise-server#reserved-names).

1. Log in to the administrative shell using SSH. For more information, see [AUTOTITLE](/admin/configuration/configuring-your-enterprise/accessing-the-administrative-shell-ssh).
1. To designate your organization as the location to store the bundled actions, use the `ghe-config` command, replacing `ORGANIZATION` with the name of your organization.

    ```shell
    ghe-config app.actions.actions-org ORGANIZATION
    ```

    and:

    ```shell
    ghe-config app.actions.github-org ORGANIZATION
    ```

1. To add the bundled actions to your organization, unset the SHA.

    ```shell
    ghe-config --unset 'app.actions.actions-repos-sha1sum'
    ```

1. Apply the configuration.

    ```shell
    ghe-config-apply
    ```

After you've completed these steps, you can resume configuring {% data variables.product.prodname_actions %} at [AUTOTITLE](/admin/github-actions/getting-started-with-github-actions-for-your-enterprise/getting-started-with-github-actions-for-github-enterprise-server#managing-access-permissions-for-github-actions-in-your-enterprise).
