# Aws Ec2 Auto Recovery

## Overview

![](./docs/overview.jpg)

## Objectives

* Monitor availability of web-sites hosted on a single EC2 instance
* Before taking any recovery action send notification email and give admin personnel chance to respond
* On first failure restart the instance and send notification email
* On second failure re-provision the instance in the same region, send email notification, and re-assign Elastic IP
* On third failure re-provision the instance in DR region and send notification email
* On subsequent failures take no action other than send an email
* In case the original instance becomes available after an outage, send notification email

## Scope

* Record monitoring status to a persistent back-end
* Deliver functionality using a suitable service on Aws
* Document deployment and testing process using markdown format
* Walk through the implementation and setup
* Assist with setup on customer environment

## Assumptions

* The monitoring is running in a single region
* When monitoring region is unavailable the monitoring system will be unavailable 
* Target EC2 instance is tagged
* Snapshots suitable for restore are available in both regions
* Snapshots are tagged and latest available will be used to re-provision the failed instance
* In case snapshot is unavailable - re-provisioning process will fail
* Non-recoverable errors will be written to log and will require human intervention

## Tentative Architecture

![](./docs/architecture.jpg)

* Persistent Storage: DynamoDB in auto-scale mode @ ~$1.50/month
* Why not S3: S3 does not support sorting and querying of documents on the server side
* CloudWatch Synthetic for availability monitoring: 100 canary runs per month are free?
* AWS lambda for processing DynamoDB updates: send email or take a recovery action, 1M executions/month is free
* SES to send email notifications, 62,000 sent email/month is free
* Route 53 could be used to chain CNAME's or Lambda function could update Revize DNS directly?
* CloudWatch for logging


## Artifacts

* [Draw.io diagram](./docs/RevizeMonitoring.drawio), can be edited using http://draw.io/