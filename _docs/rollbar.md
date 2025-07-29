## Configuring RollBar

Rollbar is a real-time error tracking platform that helps you detect, diagnose, and fix runtime errors in real time.


## Instrumenting Rollbar

1. Create an account in [Rollbar](https://rollbar.com/).
2. Create a New Project:
- **Project Name**: Enter a project name.
- **TEAM**: Select _Owners x memeber_.
3. Click **Create Project**.
4. Select **Flask** as the SDK.
5. From the code snippet copy your rollbar access token and add it in `.env`.

Add the following environment variable in `.env`:
```
ROLLBAR_ACCESS_TOKEN="your_rollbar_access_token_here"
```

Add the following packages in `requirements.txt`:
```
blinker
rollbar
```

Install the dependencies:
```
$ cd backend-flask
$ pip install -r requirements.txt
```

In `app.py` adding the folowing imports:
```
import rollbar
import rollbar.contrib.flask
from flask import got_request_exception
```

Initialize the pyroll package:
```
with app.app_context():
    """init rollbar module"""
    rollbar.init(
        # access token
        os.getenv('ROLLBAR_ACCESS_TOKEN'),
        # environment name - any string, like 'production' or 'development'
        'backend-dev',
        # server root directory, makes tracebacks prettier
        root=os.path.dirname(os.path.realpath(__file__)),
        # flask already sets up logging
        allow_logging_basic_config=False)

    # send exceptions from `app` to rollbar, using flask's signal system.
    got_request_exception.connect(rollbar.contrib.flask.report_exception, app)
```

You need at least one route that raises an exception in order to trigger Rollbar's error tracking.
Add the following test route:
```
@app.route('/rollbar/test')
def hello():
    print("DEBUG - in hello()")
    x = None
    x[5]
    return "Hello World!"
```    

Bingo! Go to your Rollbar project, and you should see the events popping up in the Dashboard.

![Rollbar Dashboard](/images/rollbar.png)
