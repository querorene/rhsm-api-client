# Red Hat RHSM APIs Python client implementation 
[![All Contributors](https://img.shields.io/badge/all_contributors-3-orange.svg?style=flat-square)](#contributors)

This project aim is to create a client interface that using Red Hat Subscription Manager (RHSM) APIs is capable to collect a series of data from your account. 

As described in the official Red Hat article [Getting started with RHSM APIs in tech preview](https://access.redhat.com/articles/3626371):

```
Using APIs in RHSM can help you more effectively keep track of and automate how you manage your Red Hat subscriptions and entitlement usage. By using APIs in RHSM, you can:

* Control which tooling you use for which products
* Better manage your system inventory
* Update and secure your systems more efficiently
* Continue receiving official support for your Red Hat products

In order to transition to using APIs for Red Hat Subscription Management, Red Hat has created a tech preview program for early access and feedback. Red Hat is in the process of decommissioning Red Hat Network (RHN), including access to its APIs. As a part of this effort, Red Hat has been developing and documenting support for RHSM.
```

# Getting Started

## Installation instructions in a virtualenv

* Clone the git repository:
```
$ git clone https://github.com/antonioromito/rhsm-api-client
```

* Create and activate the virtualenv
```
$ cd rhsm-api-client
$ virtualenv .venv
$ . .venv/bin/activate
```

* Install the required packages
```
$ pip install -r requirements.txt
```

* Install the `rhsm-api-client` tool
```
$ python setup.py install
```

The `rhsm-cli` command is now added to the `PATH` environment variable and can be executed

## Installation instruction for a system-wide install

### Prerequisites

Red Hat Subscription Management APIs use OAuth 2.0 for authorization. For this reason rhsm-api-client uses the following libs:

* Python3 package required:

    * python3-requests-oauthlib
    * python3-oauthlib
    
* Python2 package required:

    * python2-requests-oauthlib
    * python2-oauthlib

Before to start script execution, you'll need the following information:

* Your Customer Portal credentials (https://access.redhat.com/)
* Client ID and Secret provided by Red Hat (https://access.redhat.com/management/api)
 
#### Required packages installation instructions for Fedora 29:

* RPM Packages installation for Python3:
```
$ sudo yum install python3-oauth2client
$ sudo yum install python3-requests-oauthlib
```

* RPM Packages installation for Python2:
```
$ sudo yum install python2-oauth2client
$ sudo yum install python2-requests-oauthlib
```
    
#### Required packages installation instructions for RHEL 7.6 via EPEL repos:
       
* Install EPEL repo rpm
    
If you are running an EL7 version, please visit here to get the newest 'epel-release' package for EL7: [The newest version of 'epel-release' for EL7](https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm)

```
$ sudo yum localinstall https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```
    
* Install git
    
```
$ sudo yum install git
```

* Install required packages:

```
sudo yum install python2-oauth2client python2-requests-oauthlib
```

### rhsm-api-client install instructions:

* Cloning git repository:
```
$ git clone https://github.com/antonioromito/rhsm-api-client
$ cd rhsm-api-client
$ python setup.py install
```  

Now the command `rhsm-cli` can be executed from your preferred path.

## Usage

```
usage: rhsm-cli [-h] [-c CLIENT_ID] [-i IDP_TOKEN_URL] [-t TOKEN]
                [-f CONFIG_FILE]
                {systems,allocations,subscriptions,errata,packages,images,savetoken}
                ...

RHSM API implementation

positional arguments:
  {systems,allocations,subscriptions,errata,packages,images,savetoken}
                        Program mode: systems, allocations, subscriptions,
                        errata, packages, images, savetoken)
    systems             Fetch a list of systems.
    allocations         Generate allocations CSV report.
    subscriptions       Generate subscriptions CSV report.
    errata              Generate errata CSV report.
    packages            Generate packages CSV report.
    images              Download an image for a given checksum.
    savetoken           Save API token in local config file

optional arguments:
  -h, --help            show this help message and exit

authentication:
  -c CLIENT_ID, --client_id CLIENT_ID
                        Red Hat Customer Portal OIDC client (default: rhsm-api)
  -i IDP_TOKEN_URL, --idp_token_url IDP_TOKEN_URL
                        Red Hat Customer Portal SSO Token URL (default: https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token)
  -t TOKEN, --token TOKEN
                        Red Hat Customer Portal offline token
  -f CONFIG_FILE, --config_file CONFIG_FILE
                        Config file to use
```

## Offline Token Storage

Your offline token is a form of credential and should be treated securely. To help prevent
your token from being exposed in your shell history or via copy/paste, you can store your token
in a local config file. The default path for the config file is `~/.config/rhsm-cli.conf`, you
can also use a different file tanks to the `-f` main option.

The config file is in `JSON` format, here is an example:

```
{
    "token": "[...]"
}
```
Is strongly suggested to set the `600` permissions to the config file (`chmod 600 ~/.config/rhsm-cli.conf`)
You can also save the token defined via the `-t` option wih the `savetoken` action:

```
$ rhsm-cli -t $TOKEN savetoken
```

If you prefer passing the token from the command line we suggest to define an evironment variable
for that. For example, with Bash you can add it to your `~/.bashrc` file and add the following line
with your token obtained from the Customer Portal:

```bash
# RHSM API Token
export RHSM_API_TOKEN=SOME_LONG_TOKEN_HERE
```

Then when calling your scripts, the token can be recalled via the variable:
```
$ ./rhsm-cli -t $RHSM_API_TOKEN systems
```

## Examples

* Generate CSV report listing all systems

Helper for "systems" CSV function provide specific instructions on the arguments that the cli expects:

```
$ ./rhsm-cli systems -h
usage: rhsm-cli systems [-h] [-u UUID] [--include {facts,entitlements,installedProducts}] [-l LIMIT] [-f {json,json_pretty,csv}]

optional arguments:
  -h, --help            show this help message and exit
  -u UUID, --uuid UUID  The UUID of the system.
  --include {facts,entitlements,installedProducts}
                        Get details for a system specified by UUID.
  -l LIMIT, --limit LIMIT
                        The default and max number of result in a response are 100.
  -f {json,json_pretty,csv}, --format {json,json_pretty,csv}
                        The format to output data as.

```

Here below you can find some example:

* CSV report generation. "systems" command line arguments usage:

```
$ ./rhsm-cli -t $RHSM_API_TOKEN systems -f csv
```

* JSON report generation. "systems" command line arguments usage:

```
$ ./rhsm-cli -t $RHSM_API_TOKEN systems -f json
```

* JSON output in readable format generation for "systems" with "facts" gathering for a given UUID system.

```
$ ./rhsm-cli -t $RHSM_API_TOKEN systems --include facts -u {UUID} -f json_pretty
```

* Red Hat Image download starting from its checksum shown on the Red Hat Portal in download section.

```
$ ./rhsm-cli -t $RHSM_API_TOKEN images --checksum {CHECKSUM}
```


## License

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the  GNU General Public License for more details.
You should have received a copy of the GNU General Public License along with this program.  If not, see <https://www.gnu.org/licenses/>.

## Authors

* **Antonio Romito** - *Initial work* - [rhsm-api-client](https://github.com/antonioromito/rhsm-api-client)

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
<table>
  <tr>
    <td align="center"><a href="https://patrick.easte.rs"><img src="https://avatars2.githubusercontent.com/u/5381696?v=4" width="100px;" alt="Patrick Easters"/><br /><sub><b>Patrick Easters</b></sub></a><br /><a href="https://github.com/antonioromito/rhsm-api-client/commits?author=patrickeasters" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/gnaponie"><img src="https://avatars3.githubusercontent.com/u/35328841?v=4" width="100px;" alt="Giulia Naponiello"/><br /><sub><b>Giulia Naponiello</b></sub></a><br /><a href="https://github.com/antonioromito/rhsm-api-client/commits?author=gnaponie" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/andreamtp"><img src="https://avatars0.githubusercontent.com/u/5984832?v=4" width="100px;" alt="Andrea Perotti"/><br /><sub><b>Andrea Perotti</b></sub></a><br /><a href="#userTesting-andreamtp" title="User Testing">📓</a></td>
  </tr>
</table>

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
