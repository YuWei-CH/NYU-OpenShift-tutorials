# NYU OpenShift Tutorials

This repository provides a series of practical tutorials and configuration examples for deploying containerized applications on **Red Hat OpenShift**, with a focus on the NYU HPC and research environment.

## Overview

Whether you're new to OpenShift or looking to deploy apps in a university or research cloud setting, these tutorials will guide you through:

- Deploying applications with `oc` CLI and the OpenShift web console
- Building container images with Source-to-Image (S2I) and Dockerfiles
- Setting up persistent volumes and working with secrets/config maps
- Integrating OpenShift with GitHub, container registries, and external storage

## Repository Structure
```
NYU-OpenShift-tutorials/  
├── OpenShift-Intro/           # Introduction of OC commands, OpenShift console, Login Token  
├── Push-images/               # Introduction to Quay, Podmand commands, and buidling an image  
├── Deploy-job/                # How to deploy a job to OpenShift and GPU Application Example  
├── Data-transfer/             # OC copy, rsync command, and how to use linux share command on OpenShift
├── Migrate-project/           # Migrate a project(environment) from Greene to Openshift
├── README.md                  # You’re here!  
```
## Prerequisites

- NYU VPN Access
- Access to an OpenShift cluster (e.g., [NYU OpenShift Platform](https://console.cloud.rt.nyu.edu/))
- OpenShift CLI (`oc`) installed and configured
- Familiarity with Kubernetes concepts (Pods, Services, Deployments)

## Notes

- These examples are tailored for NYU’s HPC research community, but can be adapted for general OpenShift usage.
- All rights reserved to NYU RIT.
