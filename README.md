# Welcome to the SOAFEE AWS IoT Fleetwise demo

The repository contains the artifacts and code that allows you to reproduce the demo shown in the session [**Achieving environmental parity through multiple layers of abstractions helps to support AWS IoT FleeWise edge on EWAOL**](https://www.youtube.com/watch?v=Wd1isAmTtp8) at [SOAFEE Virtual Symposium 2022](https://soafee.io/blog/2022/virtual_symposium/).

The demo will walk you through the exercise of running on [EWAOL](https://github.com/aws4embeddedlinux/meta-aws-ewaol) the [AWS IoT FleeWise](https://aws.amazon.com/iot-fleetwise/) Edge. The [AWS IoT FleeWise Edge](https://github.com/aws/aws-iot-fleetwise-edge) will run in a container and the orchestration will be done with [k3s](https://k3s.io/). We can use the exact same container image both in the cloud and on a physical target, as long as they are based on an ARM v8 core. We also show how the [cdk8s](https://cdk8s.io/) framework can improve the software development process abstracting the orchestration layer.

> TODO: Add Architecture

> :warning: **At the time of writing, AWS Iot FleetWise is only available in preview only in us-east-1 and eu-central-1 so be sure to use one of the mentioned region to run the demo on**

## Getting started

Create Build Host based on AWS Graviton and access to it via ssh.

> TODO: provide a one click deployment for an EC2 with with a minimum of 100G storage if you want to build an EWAOL Image and admin role

Clone this repository

```sh
git clone https://github.com/aws-samples/demo-soafee-aws-iotfleetwise
cd demo-soafee-aws-iotfleetwise
```

Install and bootstrap cdk

```sh
npm install -g aws-cdk
npx cdk bootstrap --cloudformation-execution-policies \
  arn:aws:iam::aws:policy/AdministratorAccess 
```

Run the following script to deploy the cdk stack that will deploy all the cloud resources as shown on the architeture above.

```sh
./script/deploy-cloud.sh
```

### Get AWS FleetWise Edge running on the Build Host

This is the quickest option to see AWS IoT FleetWise running without having to build the EWAOL AMI and power up an AWS Graviton instance with it.

We will be using the orchestrator (k3s) used in EWAOL. So let's get started by installing it

```sh
curl -sfL https://get.k3s.io | sh -
```

Build the Vehicle Simulator container image that will feed CAN Bus Data in to the AWS IoT FleetWise Edge

```sh
./script/build-vsim.sh
```

Load certificate and key into k3s secrets

```sh
kubectl create secret generic private-key --from-file=./.tmp/private-key.key
kubectl create secret generic certificate --from-file=./.tmp/certificate.pem
```

Deploy the kubernetes manifest to k3s

```sh
./script/deploy-k3s.sh
```

Now you can connect to the Vehicle Simulator Webapp, change things like opening the doors and observe the data signals values being fed into our Amazon Timestream table.

### Get AWS FleetWise Edge running on an EWAOL Virtual Target 

TODO

### Get AWS FleetWise Edge running on an EWAOL Physical Target 

TODO

---

This repository depends on and may incorporate or retrieve a number of third-party
software packages (such as open source packages) at install-time or build-time
or run-time ("External Dependencies"). The External Dependencies are subject to
license terms that you must accept in order to use this package. If you do not
accept all of the applicable license terms, you should not use this package. We
recommend that you consult your company’s open source approval policy before
proceeding.

Provided below is a list of External Dependencies and the applicable license
identification as indicated by the documentation associated with the External
Dependencies as of Amazon's most recent review.

THIS INFORMATION IS PROVIDED FOR CONVENIENCE ONLY. AMAZON DOES NOT PROMISE THAT
THE LIST OR THE APPLICABLE TERMS AND CONDITIONS ARE COMPLETE, ACCURATE, OR
UP-TO-DATE, AND AMAZON WILL HAVE NO LIABILITY FOR ANY INACCURACIES. YOU SHOULD
CONSULT THE DOWNLOAD SITES FOR THE EXTERNAL DEPENDENCIES FOR THE MOST COMPLETE
AND UP-TO-DATE LICENSING INFORMATION.

YOUR USE OF THE EXTERNAL DEPENDENCIES IS AT YOUR SOLE RISK. IN NO EVENT WILL
AMAZON BE LIABLE FOR ANY DAMAGES, INCLUDING WITHOUT LIMITATION ANY DIRECT,
INDIRECT, CONSEQUENTIAL, SPECIAL, INCIDENTAL, OR PUNITIVE DAMAGES (INCLUDING
FOR ANY LOSS OF GOODWILL, BUSINESS INTERRUPTION, LOST PROFITS OR DATA, OR
COMPUTER FAILURE OR MALFUNCTION) ARISING FROM OR RELATING TO THE EXTERNAL
DEPENDENCIES, HOWEVER CAUSED AND REGARDLESS OF THE THEORY OF LIABILITY, EVEN
IF AMAZON HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES. THESE LIMITATIONS
AND DISCLAIMERS APPLY EXCEPT TO THE EXTENT PROHIBITED BY APPLICABLE LAW.


vsim/Dockerfile depends on third party **docker/library/node** container image, please refer to [license section](https://gallery.ecr.aws/docker/library/node) 

vsim/Dockerfile depends on third party **docker/library/python** container image, please refer to [license section](https://gallery.ecr.aws/docker/library/python) 