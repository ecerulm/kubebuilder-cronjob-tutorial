
This is just the result of following along the [Tutorial 1: Building CronJob][1]



# kubebuilder-cronjob-tutorial

https://book.kubebuilder.io/cronjob-tutorial/cronjob-tutorial

## Step 1


```
# create a project directory, and then run the init command.
mkdir project
cd project
# we'll use a domain of tutorial.kubebuilder.io,
# so all API groups will be <group>.tutorial.kubebuilder.io.
kubebuilder init --domain tutorial.kubebuilder.io --repo tutorial.kubebuilder.io/project

```


The result is in tag `V1` in this repo

    git checkout V1


# Step 2: Modify project to add cont


[1]: https://book.kubebuilder.io/cronjob-tutorial/cronjob-tutorial
