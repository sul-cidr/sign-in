# CIDR Workshop Forms

This repo contains a wrapper for the sign-in and evaluation forms used for CIDR workshops.  The wrapper allows automatic provisioning of forms for workshops, by pre-filling the workshop field based on the URL.  Responses for all workshops are collected in two Google Forms instances (one for sign-ins and one for evaluations).

Forms are in the CIDR Team Drive: https://drive.google.com/drive/u/1/folders/1gJQpoc4462k2ddQrmHvNCeoNLqBZpO1N.

The wrapper is deployed via github pages, and intended to be accessed at `https://signin.cidr.link/<workshop>/` (note the trailing slash) and `https://evaluations.cidr.link/<workshop>/`.  The `workshop` parameter passed to the forms in addition to being used to write the subtitle.

The raw github pages URL is https://sul-cidr.github.io/workshop-forms/, where the wrapper can be configured at access time by providing either `signin` or `evaluation` as a query string parameter, along with a `workshop` parameter if necessary.

The NGINX server which serves signin.cidr.link and evaluations.cidr.link uses a reverse proxy and HTML sub-filter configuration that looks like this (for `signin.cidr.link`):
```
  location / {
    rewrite ^/([^/]+)?/?$ /workshop-forms/ break;
    proxy_pass https://sul-cidr.github.io;
    proxy_set_header Accept-Encoding "";
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    sub_filter '/* global _params */' 'const _params = { workshop: "$1", formType: "signin", nextUrl: "//evaluations.cidr.link/$1/" };';
    sub_filter_once on;
  }
```
