# aws-ec2-scum

Run a [SCUM](https://www.scum.game) game server in a AWS [EC2](https://aws.amazon.com/ec2) instance.

## Dependencies

- [AWS CLI](https://aws.amazon.com/cli)

### AWS requirements

In order to successfully deploy your application you must have [set-up your AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-cli.html) and have [created an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) with the following [policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage.html):

- [IAMFullAccess](https://console.aws.amazon.com/iam/home#/policies/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FIAMFullAccess)
- [AmazonEC2FullAccess](https://console.aws.amazon.com/iam/home#/policies/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAmazonEC2FullAccess)

WARNING: The policies above are provided to ensure a successful EC2 deployment.  It is recommended that you adjust these policies to meet the security requirements of your game server.  They should NOT be used in a Production environment.

## Launching the EC2 instance

    $ aws ec2 run-instances --image-id ami-0150ccaf51ab55a51 --instance-type t3.xlarge --region us-east-1a --block-device-mappings file://block-device-mapping.json --user-data file://user-data.sh --associate-public-ip-address

## Logging into your server

As part of the installation process an [SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/prereqs-ssm-agent.html) is added which allows you to access your server using the [Amazon EC2 Console](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-sessions-start.html#start-ec2-console).  No SSH keys, port 22 routing necessary.

### Accessing the container

    $ docker exec -it <container-id> /bin/bash

## Game server defaults

The container comes with a vanilla installation of [SCUM Dedicated Server](https://steamdb.info/app/3792580) which is configured to support _up to 3 players_ and broadcasts the server name "SCUM Server".  The following ports need to be opened using a [EC2 Security Group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#creating-security-group) policy.

### Ports to open

| Port | Protocol | Description                                      |
|------|----------|--------------------------------------------------|
| 7777 | tcp      | RCON for remote console server access (optional) |
| 7777 | udp      | Game client port                                 |
| 7778 | udp      | Raw UDP socket port                              |
| 7779 | tcp/udp  | Query port for Steam's server browser            |

## Managing the game server

The following command can be executed within the Docker container:

    $ service game-server {start|stop|restart}

## Overriding game sources

In cases where you have an existing game set-up (e.g. [INI configurations](https://steamcommunity.com/sharedfiles/filedetails/?id=2223029503)) follow the steps below:

### Copy the files to the container

    $ docker cp ServerSettings.ini <container-id>:/usr/games/Steam/steamapps/common/SCUM\ Server/SCUM/Saved/Config/WindowsServer/ServerSettings.ini
    $ docker cp *.ini <container-id>:/usr/games/Steam/steamapps/common/SCUM Server/SCUM/Saved/Config/WindowsServer

### Update the file permissions

    $ docker -it <container-id> /bin/chown games:games /usr/games/Steam/steamapps/common/SCUM\ Server/SCUM/Saved/Config/WindowsServer/*.ini

### Restart the game server

    $ docker -it <container-id> /usr/sbin/service game-server restart

Mirroring that of the existing game directory, files that already exist will be overwritten.

## References

- [What is Amazon Linux 2023](https://docs.aws.amazon.com/linux/al2023/ug/what-is-amazon-linux.html)
- [SCUM game server list](https://www.battlemetrics.com/servers/scum)

## Contributions

If you fix a bug, or have a code you want to contribute, please send a pull-request with your changes.

## Versioning

This package is maintained under the [Semantic Versioning](https://semver.org) guidelines.

## License and Warranty

This package is distributed in the hope that it will be useful, but without any warranty; without even the implied warranty of merchantability or fitness for a particular purpose.

_aws-ec2-scum_ is provided under the terms of the [MIT license](http://www.opensource.org/licenses/mit-license.php)

[AWS](https://aws.amazon.com) is a registered trademark of Amazon Web Services, Inc.

## Author

[Marc S. Brooks](https://github.com/nuxy)
