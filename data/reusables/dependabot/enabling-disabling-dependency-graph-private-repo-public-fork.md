Repository administrators can enable or disable the dependency graph for private {% ifversion ghec %}or internal{% endif %} repositories, or public forks.

You can enable or disable the dependency graph for all repositories owned by your user account. For more information, see [AUTOTITLE](/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-user-account-settings/managing-security-and-analysis-settings-for-your-personal-account).

You can also enable the dependency graph for multiple repositories in an organization at the same time. For more information, see {% ifversion security-configurations %}[AUTOTITLE](/code-security/securing-your-organization).{% else %}[AUTOTITLE](/code-security/getting-started/quickstart-for-securing-your-organization).{% endif %}

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
{% data reusables.repositories.navigate-to-code-security-and-analysis %}
1. Read the message about granting {% data variables.product.github %} read-only access to the repository data to enable the dependency graph, then next to "Dependency Graph", click **Enable**.

   You can disable the dependency graph at any time by clicking **Disable** next to "Dependency Graph" on the settings page for "{% data variables.product.prodname_AS %}".
