
**_90% of the effort required for success in machine learning is not the algorithm or the model or the learning — it’s the logistics._**

Red Hat OpenShift provides excellent tooling on top of Kubernetes that reduces Time to Market and reduces logistical challenges. Red Hat also has partnership with Nvidia that makes containerized GPU workloads possible in OpenShift, leveraging the Device Plugin feature in OpenShift.

![Image for post](https://miro.medium.com/max/60/1*BzA6IYpTvnWbOcc0o9ck6Q.png?q=20)

![Image for post](https://miro.medium.com/max/1302/1*BzA6IYpTvnWbOcc0o9ck6Q.png)

Now coming back to Deploying Deep Learning Model on OpenShift.

If you have seen my recent  [blog](https://link.medium.com/qM4CSsMqgR)  post, we have a constant need to iterate on models and improve those.

![Image for post](https://miro.medium.com/max/60/1*DwobWDylpt_PX6MO0MXIlg.png?q=20)

![Image for post](https://miro.medium.com/max/1422/1*DwobWDylpt_PX6MO0MXIlg.png)

Faster and more testing is needed to mature these models.

Containers and Openshift can be of good help here as you can deploy multiple models in parallel and route request — splitting requests say 50% to each model — to these models to see how they behave.

Lets take a Deep Learning Model and deploy it on OpenShift.

We will take Keras and build Deep Learning model with it. We can wrap it with Rest API and deploy on OpenShift.

I will use the code provided at github[1] by Adrian Rosebrock.

This uses ResNet50 architecture which has been pre-trained on the ImageNet dataset.

Pre-trained model helps us to save a lot of time and is very beneficial as I do not have to use compute to get this going.

You can take this code and convert it to image. The image is now ready and available in Dockerhub at_vikasgrover2004/keras-app_ [(https://hub.docker.com/r/vikasgrover2004/keras-app/)](https://mojo.redhat.com/external-link.jspa?url=https%3A%2F%2Fhub.docker.com%2Fr%2Fvikasgrover2004%2Fkeras-app%2F%29)

Once your image is ready, you can work to get this deployed on OpenShift.

Here are the steps for anyone who is interested to play with this model and run it on OpenShift.

_$oc run keras-app1 — image=vikasgrover2004/keras-app — port=5000_

**_deploymentconfig “keras-app1” created_**

_$oc get pods_

**NAME READY STATUS RESTARTS AGE**

**keras-app1–1-wg26g 1/1 Running 0 1m**

_$oc expose dc keras-app1 — type=LoadBalancer — port 80 — target-port 5000_

**service “keras-app1” exposed**

Now the your Pod is up and running and service is ready, You can use OpenShift console to Create Route (or use oc command)

Even though you have exposed the route, you will not be able to access it using web browser because exposed /predict only accepts a POST.

So, curl will help here and we can send the image to this model and see what predictions it provides us

For eg: I provided a image of car and got following predictions which were not that impressive.

But I provided image of Tiger and it was pretty good result.

![Image for post](https://miro.medium.com/max/60/1*TOQay7NAptshb5mpeEZWsQ.png?q=20)

![Image for post](https://miro.medium.com/max/830/1*TOQay7NAptshb5mpeEZWsQ.png)

curl -X POST -F image=@car.jpg ‘[http://keras-app1-mlprj1.127.0.0.1.nip.io/predict](https://mojo.redhat.com/external-link.jspa?url=http%3A%2F%2Fkeras-app1-mlprj1.127.0.0.1.nip.io%2Fpredict)'

{“predictions”:[{“label”:”grille”,”probability”:0.2803921401500702},{“label”:”pickup”,”probability”:0.25131097435951233},{“label”:”racer”,”probability”:0.18713875114917755},{“label”:”sports_car”,”probability”:0.14187376201152802},{“label”:”car_wheel”,”probability”:0.044908713549375534}],”success”:true}

![Image for post](https://miro.medium.com/max/60/1*zKksYU_E5RsDMW3FrUhkfg.png?q=20)

![Image for post](https://miro.medium.com/max/724/1*zKksYU_E5RsDMW3FrUhkfg.png)

curl -X POST -F image=@Tiger.jpg ‘[http://keras-app1-mlprj1.127.0.0.1.nip.io/predict](https://mojo.redhat.com/external-link.jspa?url=http%3A%2F%2Fkeras-app1-mlprj1.127.0.0.1.nip.io%2Fpredict)'

{“predictions”:[{“label”:”tiger”,”probability”:0.860263466835022},{“label”:”tiger_cat”,”probability”:0.1142442524433136},{“label”:”zebra”,”probability”:0.011854393407702446},{“label”:”jaguar”,”probability”:0.004141010344028473},{“label”:”lynx”,”probability”:0.002418165560811758}],”success”:true}

Now going back to initial point, where you may need to do various iterations, you can make changes in model, deploy, use OpenShift features to split traffic in different versions and test to see how your model is improving.

![Image for post](https://miro.medium.com/max/60/1*cxztWRNN-9y4NMaVXIDDcw.png?q=20)

![Image for post](https://miro.medium.com/max/1504/1*cxztWRNN-9y4NMaVXIDDcw.png)

1]  [https://github.com/jrosebr1/simple-keras-rest-api](https://mojo.redhat.com/external-link.jspa?url=https%3A%2F%2Fgithub.com%2Fjrosebr1%2Fsimple-keras-rest-api)
