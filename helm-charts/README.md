# Helm Charts

### **Ever wonder how to make a Helm Chart?**

That's actually pretty easy, just run `helm create my-awesome-chart` and it will create a folder with the name `my-awesome-chart` with a lot of great boilerplate configuration ready to go out of the box.

### **How do I test my Helm Charts?**

In the development cycle when you're maybe testing new functional logic, you can see what your applied Helm Chart and the provided values would equate to by running `helm template my-awesome-chart --values=optional-values.yml` - you can append `> my-applied-chart.yml` to the end of that command to direct the output into the `my-applied-chart.yml` file instead of being displayed in your terminal.

### **Where do I store the Helm Charts?**

Well, similar to many other YAML-y things, you store the Helm Chart manifests in a Source Code Management tool such as Git.

### **What about managing multiple Charts?**

You may notice that for some Helm Chart providers, they have a Helm Repository that they provide to their users - it's from this Helm Repository that multiple Helm Charts can be installed from.

All you need is a simple web server and some metadata in a YAML file to glue things together - it could even be stored on GitHub Pages, an S3 bucket, etc.  The directory structure looks like:

```
📂 /
 ┣ index.yaml
 ┣ my-awesome-chart.tgz
 ┣ my-awesome-chart.tgz.prov
```

The `index.yaml` file is just some simple metadata that houses charts and their versions - thankfully, you don't have to create it from scratch, it's part of the release bundling process.

```bash
# Create a directory
mkdir helm-repository

# Package a Helm Chart release
helm package omg-shoes

# Move the package into the repository-directory
mv omg-shoes-*.tgz helm-repository/

# Create the repository index
helm repo index helm-repository --url https://helm-charts.example.com

# Merge additional charts/releases into an existing index
helm repo index helm-repository --merge --url https://helm-charts.example.com

# Add the remote repository
helm repo add example-charts https://helm-charts.example.com
```

> You can read more about the Helm Chart Repository here: https://helm.sh/docs/topics/chart_repository/