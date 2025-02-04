In this repo, we used flask app which accepts image and returns prediction.

Once you run the app, it renders home.html and it accepts the image and calls /predict method.

It does the predictions and sends back the results and the image back to same html.

### It has been also deployed to Heroku

Heroku URL : https://emlopsinfy.herokuapp.com/

Steps to Heroku Deployment :

https://www.python-engineer.com/posts/pytorch-model-deployment-with-flask/

Heroku local wont work in windows.

For production we want to have a proper web server, so we install *gunicorn*:

```
$ pip install gunicorn
```

All the following files should be added to the base directory. First, create a *wsgi.py* file and insert this line

```
from app.main import app
```

Create a *Procfile* and insert this:

```
web: gunicorn wsgi:app
```

Modify path names to take the *app* package as base:

```
# in the main.py file:
from app.torch_utils import get_prediction, transform_image

# in the torch_utils.py file
PATH = "app/mnist_ffn.pth"
```

Create a *runtime.txt* and insert the Python version you are using:

```
python-3.8.3
```

Make sure you are in the root folder of your package again. Now add all the packages to the *requirements.txt* file using `pip freeze`:

```
$ pip freeze > requirements.txt
```

Since we only can use the CPU version, modify the file like this to use PyTorch's CPU-only version. The command for CPU-only version can be taken from the PyTorch installation guide [here](https://pytorch.org/get-started/locally/). Select Linux, pip, and CUDA None. The download command may be added as first line in your *requirements.txt* file:

```
-f https://download.pytorch.org/whl/torch_stable.html
torch==1.6.0+cpu
torchvision==0.7.0+cpu
```

Add a *.gitignore*. You can take this version for Python from [GitHub](https://github.com/github/gitignore/blob/master/Python.gitignore). Also add the testing folder, so the file may have this as first lines:

```
test/

# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class
...
```

Create a Heroku app. For this you need to have the Heroku CLI installed. You can get it [here](https://devcenter.heroku.com/articles/heroku-cli). Login and then create a new app with the name you want:

```
$ heroku login -i
$ heroku create your-app-name
```

Test your app locally:

```
$ heroku local
```

Now add a git repository, commit all the files, and push it to Heroku:

```
git init
heroku git:remote -a your-app-name
git add .
git commit -m "initial commit"
git push heroku master
```

This should deploy your app to Heroku and will show you the link to your live running app. Now let's use this url in the *test.py* file like this:

```
import requests

resp = requests.post("https://your-app-name.herokuapp.com/predict",
                     files={"file": open('eight.png','rb')})

print(resp.text)
```

Congratulations! You now have a live running app with a PyTorch model that can do digit classification! Note that the first time we send a request this may take a few seconds, since Heroku has to wake up our app first if we only use the free tier.

I hope you enjoyed this tutorial!



