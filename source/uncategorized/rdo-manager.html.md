---
title: RDO-Manager
authors: athomas, hewbrocca, jcoufal, jistr
wiki_title: RDO-Manager
wiki_revision_count: 49
wiki_last_updated: 2015-07-23
---

# RDO-Manager

## About

RDO-Manager is an OpenStack Deployment & Management tool for RDO. It combines the best from the [OpenStack TripleO](http://wiki.openstack.org/wiki/TripleO) and [SpinalStack](http://spinal-stack.readthedocs.org/en/latest/) projects.

RDO-Manager Architecture Overview: <http://www.rdoproject.org/RDO_Manager_Architecture_Overview>

RDO-Manager Repositories: <http://github.com/rdo-management>

## Inlunch

Inlunch is a tool which runs [Instack](http://www.rdoproject.org/RDO-Manager#Instack) while you are having lunch. In other words, it automates Instack scripts. Maybe more importantly, it prepares a virtual environment so that you can run Instack and it will find the virtual machines it needs to set up RDO Manager and the Workload Cloud.

GitHub: <http://github.com/rdo-management/inlunch>

## Instack

Instack is a tool which installs RDO Manager, also known as the "Deployment Cloud" or the "Undercloud".

Instack also includes scripts to use CLI commands to deploy a Workload Cloud or "Overcloud" layer (the OpenStack cloud you want to actually run instances on).

GitHub: <http://github.com/rdo-management/instack-undercloud>

Usage Guide: <http://repos.fedorapeople.org/repos/openstack-m/instack-undercloud/html/index.html>

## Tuskar API

Tuskar is a service which allows users to define a workload cloud before deploying it via Heat, and then to scale out a deployed workload cloud by updating the definition.

Tuskar Plan REST API Specification: <http://specs.openstack.org/openstack/tripleo-specs/specs/juno/tripleo-juno-tuskar-rest-api.html>

Tuskar API examples: <http://www.rdoproject.org/Tuskar-API>

## Presentations

Demo 3 (March 9, 2015)

*   [RDO-Manager Deployment Flow (non-narrated)](http://youtu.be/zKG-CB8WdTg)
