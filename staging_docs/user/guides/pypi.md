# Setup your own PyPI:

This section guides you through the quickest way to setup `pulp_python` to act as your very own
private `PyPI`.


## Create a Repository:

Repositories are the base objects `Pulp` uses to store and organize its content. They are automatically
versioned when content is added or deleted and allow for easy rollbacks to previous versions.

=== "Run"

    ```bash
    # Start by creating a new repository named "foo":
    pulp python repository create --name foo
    ```

=== "Output"

    ```
    {
         "pulp_href": "/pulp/api/v3/repositories/python/python/3fe0d204-217f-4250-8177-c83b30751fca/",
         "pulp_created": "2021-06-02T14:54:53.387054Z",
         "versions_href": "/pulp/api/v3/repositories/python/python/3fe0d204-217f-4250-8177-c83b30751fca/versions/",
         "pulp_labels": {},
         "latest_version_href": "/pulp/api/v3/repositories/python/python/3fe0d204-217f-4250-8177-c83b30751fca/versions/1/",
         "name": "foo",
         "description": null,
         "retained_versions": null,
         "remote": null,
         "autopublish": false
     }
    ```

## Create a Distribution:

Distributions serve the content stored in repositories so that it can be used by tools like `pip`.

=== "Run"

    ```bash
    pulp python distribution create --name my-pypi --base-path my-pypi --repository foo
    ```

=== "Output"

    ```
    {
      "pulp_href": "/pulp/api/v3/distributions/python/pypi/e8438593-fd40-4654-8577-65398833c331/",
      "pulp_created": "2021-06-03T20:04:18.233230Z",
      "base_path": "my-pypi",
      "base_url": "https://pulp3-source-fedora33.localhost.example.com/pypi/foo/",
      "content_guard": null,
      "pulp_labels": {},
      "name": "my-pypi",
      "repository": "/pulp/api/v3/repositories/python/python/3fe0d204-217f-4250-8177-c83b30751fca/",
      "publication": null,
      "allow_uploads": true
    }
    ```

## Upload and Install Packages:

Packages can now be uploaded to the index using your favorite Python tool. The index url will be available
at `/pypi/<distribution.base_path>/simple/`.

```bash
# Build custom package
python -m build $PLUGIN_SOURCE
# Upload built package distributions to my-pypi
twine upload --repository-url $BASE_ADDR/pypi/my-pypi/simple/ -u admin -p password "$PLUGIN_SOURCE"dist/*
```

Packages can then be installed using your favorite Python tool:

```bash
pip install --trusted-host localhost -i $BASE_ADDR/pypi/my-pypi/simple/ shelf-reader
```

Now you have a fully operational Python package index. Check out the other workflows to see more features of
`pulp_python`.
