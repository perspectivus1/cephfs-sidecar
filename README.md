# Performant loading of initial content across namespaces

## Use case
* We offer a Kubernetes-based solution, in which individual end-users are given access to a single container within a single pod.
* That end-user-facing container contains initial content (files), that is identical for all users.
* The initial content seems to the end-user as read/write; however, any changes made by users to the initial content:
    * are not persisted
    * do not affect other users' initial content

## Hard requirements
* Multiple pods across multiple namespaces should have access to the same initial content.
* The pods should be ready in a timely manner.
* The solution must *not* run the end-user container in privileged security context

## Soft requirements
* The solution should avoid running any sidecar containers in privileged security context

## Existing solution
We currently run a sidecar container that copies initial content to a shared `emptyDir`. This is time- and resource-consuming, and redundant, as the initial content is identical across all namespaces/pods.

## Proposed solution
Maintain a single "source-of-truth" of initial content across the entire cluster, and mount the content when we spin up a user-facing pod.

## This repo
The `consumer1` and `consumer2` directories contain deployments that share the preinstalled files through a dynamically pre-provisioned [rook-cephfs](https://rook.io/docs/rook/v1.10/Storage-Configuration/Shared-Filesystem-CephFS/filesystem-storage/#shared-volume-creation) pv.

## Summary
The solution in this repo runs a sidecar in privileged security context. We are searching for a solution that would involve no sidecars.
