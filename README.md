# canonical/setup-devstack-swift

This action is used to set up an ephemeral OpenStack Swift service or S3 service using devstack.

## Usage

```yaml
      - name: Setup Devstack Swift
        id: setup-devstack-swift
        uses: canonical/setup-devstack-swift@v1
      - name: Use OpenStack Object Storage
        run: |
          echo "${{ steps.setup-devstack-swift.outputs.credentials }}" > openrc
          . openrc
          openstack container create test
```

## API

### Inputs

#### `inputs.s3api`

**Options:** `"true" | "false"`

**Default:** `"false"`

Enable swift s3api middleware to emulate the S3 REST api on top of swift. 

#### `inputs.use-cache`

**Options:** `"true" | "false"`

**Default:** `"true"`

By default, `setup-devstack-swift` will use GitHub action cache to speed up the build process.
`setup-devstack-swift` will use 1 GiB of cache space.
Sometimes, when other slower processes that need the extra cache space.
Or the GitHub workflow runs very infrequently, 
cache will be expired after 7 days for every workflow run.
GitHub action cache can be disabled using this option and save some time.

Time comparison:
| Action                                      	| Time        	|
|---------------------------------------------	|-------------	|
| Build from scratch and generate cache       	| 10 minutes  	|
| Build from scratch without generating cache 	| 6.5 minutes 	|
| Resume from cache                           	| 1.5 minutes 	|

#### `inputs.network`

**Default:** `"10.100.115.2/24"`

The network address for the devstack server and subnet reserved for the devstack container. 
Network prefix bits must less than or equal to 30
since a network address needs to be allocated to host.
Only change this option when the network address conflicts with other services.

### Outputs

#### `outputs.credentials`

Credentials used to access OpenStack swift services. 
By default, it's in rc file format i.g. `export <env-name>=<env-value>`.
Here's an example of the `credentials` output:

```bash
export OS_REGION_NAME=RegionOne
export OS_PROJECT_DOMAIN_ID=default
export OS_CACERT=
export OS_AUTH_URL=http://10.100.115.2/identity
export OS_TENANT_NAME=demo
export OS_USER_DOMAIN_ID=default
export OS_USERNAME=demo
export OS_VOLUME_API_VERSION=3
export OS_AUTH_TYPE=password
export OS_PROJECT_NAME=demo
export OS_PASSWORD=nomoresecret
export OS_IDENTITY_API_VERSION=3
```

When `s3api` is enabled, it will be an ini file similar to an AWS credential file.
Here's an example of the `credentials` output when `s3api` is set to `'true'`:

```
[default]
aws_access_key_id = 1a4f188310a34bbba35476cff72069e1
aws_secret_access_key = ac95cfa6433f41a5b6929788a88fc58a
endpoint_url = http://10.100.115.2:8080
``` 

### Examples

[Basic Usage](.github/workflows/test.yaml)

[S3 API Emulation](.github/workflows/test-s3api.yaml)