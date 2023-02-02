# Applications

No cluster is complete without a set of applications to deploy and demonstrate value with!

## Infinite Mario

Infinite Mario is a video game that works in a web browser.  It's similar to Mario but the worlds randomly generate in order to avoid that *sticky copyright business*.

It's not an application that I personally built or anything, just something cool I found online that works in a container.

There are **simple Kubernetes-native YAML manifests** in the `applications/infinite-mario/manifests/` folder that can be `kubectl/oc apply -f`'d if that's what you'd like to do.

A **Kustomization** file is also available in the `applications/infinite-mario/` folder that can be applied with `kubectl/oc apply -k` for a little extra logic handling if that's your thing.

You can also find the application distributed via a RHACM Application that can be found in `rhacm/applications/app-infinite-mario/`

## OMG Shoes

OMG Shoes is a simple and fun app, shows a set of spinning shoes in GIF format with their Nike Custom link in a QR code below.  Just a little splash of HTML, touch of CSS, and we're done.

You can find the simple source code in the `applications/omg-shoes/site/` folder along with an adjacent `Dockerfile`, and everything from an ArgoCD Application, RHACM AppSub manifests, and even just normal YAML manifests (that are synced by the other two sets of manifests).  There is also a live image available at quay.io/kenmoini/omg-shoes.

This application is synced down to the clusters via a RHACM AppSub in `rhacm/applications/app-omg-shoes/` that syncs down the PolicyGenerator in `rhacm/policy-generators/app-omg-shoes/` - which distributes an ArgoCD Application to sync it down to the individually targeted clusters.