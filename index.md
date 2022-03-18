## Welcome to Auto Coder

This is a suite of APIs to help speed up developers day to day lives with an AI assistant, powered by GPT-3, google cloud functions, tensorflow and more.

I aim to integrate much of this with IDEs like VS code (some is already) and github pull request workflows and more. 
Here the apis are documented that you can try out.

### Explain code you don't understand

With this API you provide a chunk of code, and auto coder will explain what it does in natural language. For example:

> ```curl --request POST --header 'Content-Type: application/json' --url https://us-east1-micprojects.cloudfunctions.net/ai-code-explainer --data '{"code":"for i in range(10):\n    print(i)\n"}'```

This will explain the code as best it can with the context given and its knowledge. You can include any existing comments and context you like. 

> The code prints out the numbers from 0 to 9.

### Look at code for security issues 

The AI can look for security problems you can accidentally slip in. For example: 

> ```curl --request POST --header 'Content-Type: application/json' --url https://us-east1-micprojects.cloudfunctions.net/ai-security-scan  -d '{"code": "class Log:\ndef __init__(self, path):\n        dirname = os.path.dirname(path)\n       os.makedirs(dirname, exist_ok=True)\n "}'```

Will return: 

>  The code is vulnerable to directory traversal.

Yeah probably should fix that!

### Generate unit tests 

Ever said "I'll write some unit tests when I get time" - well the auto coder has your back: 

For a trivial example: 

> ```curl --request POST --header 'Content-Type: application/json' --url https://us-east1-micprojects.cloudfunctions.net/ai-auto-test --data '{"code":"def calc(a,b):\n return a+b\n", "language":"Python"}'```

```
def test_calc():
    assert calc(1,2) == 3   
```

It works for multiple languages and more complex, less contrived snippets of code too (give it a try). 

### Github App to automatically add tests

For the lasiest people of all: <a href='https://github.com/apps/cloudbees-autotest'>this github app</a> when installed will look for new code, and if there isn't a test as part of a PR, try to add one for you (as a new commit on that PR). I recommend installing it on just one repo at a time.


### Write a pipeline from a natural language description

Try out <a href='https://pipeline-maker-ai.com/'>this tool</a> which is hosted as a google cloud run app, to write pipelines for you from natural language.

### Remove old feature flags from your code

Feature flags are if statements that litter your code over time. Eventually they end up all turned on, so you need to go back and remove them. 
Try using auto coder to help you out: 

> ```curl --data-binary @src/services/Rox.ts "https://us-east1-micprojects.cloudfunctions.net/auto-flag-remover?flag_name=showDeleteExperimentButtonInNewFlagsView" -o src/services/Rox.ts```

This will update one file at a time, but if you are using github, you can use a Personal Access Token and have it update your org all in one hit, opening pull requests for each repository that needs a change: 

> ```https://us-east1-micprojects.cloudfunctions.net/auto-flag-remover?flag_name=showDeleteExperimentButtonInNewFlagsView&org=michaelneale&token=YOUR_GITHUB_PERSONAL_ACCESS_TOKEN```


### Automatically control feature flags based on monitoring in production

With autopilot for feature flags, you can have it monitor an environment (say production) for anomalies and roll back any recently deployed features. 

See this <a href='https://rollout-autopilot.com/'>Autopilot</a> for how to use this.

### Write code from a description alone

This service will help you write code in a given language, based on a description, as a starting point:

> ```curl --request POST --header 'Content-Type: application/json' --url https://us-east1-micprojects.cloudfunctions.net/ai-code-author --data '{"description": "Write a class that gets the current memory utilisation", "language":"Java"}' ```

Once you have some code, you can help you keep writing code with: 

> ```curl --request POST --header 'Content-Type: application/json' --url https://us-east1-micprojects.cloudfunctions.net/ai-code-author --data '{"description": "filter the list of days to only include days that have a consonant in their name", "code":"days = [\"Monday\", \"Tuesday\"]"}' ```


### Solve a problem from a description

(AKA cheating on a coding test).

The Toy Robot is a popular coding test in these parts. A description of it is <a href='https://gist.githubusercontent.com/michaelneale/ba3a0fcfdf2d86525a2e12e68e36f47e/raw/a91d8df00a658371c7c74a02277df2185fd60cb2/gistfile1.txt'>here</a>

You can turn that description, as is, into code:

```python
import requests
url = 'https://us-east1-micprojects.cloudfunctions.net/ai-code-author'
x = requests.post(url, json = {'language': 'python', 'description': requests.get("https://gist.githubusercontent.com/michaelneale/ba3a0fcfdf2d86525a2e12e68e36f47e/raw/a91d8df00a658371c7c74a02277df2185fd60cb2/gistfile1.txt").content.decode('utf-8')})
open("generated_app.py", 'wb').write(x.content)
```

### Check if a chunk of code is using a feature flag or not

> ```curl --request POST --header 'Content-Type: application/json' --url https://us-east1-micprojects.cloudfunctions.net/ai-flag-detector --data '{"code":"def calc(a,b):\n    return a+b\n"}'```



### A more complex example of explaining code 

> ```curl --request POST --header 'Content-Type: application/json' --url https://us-east1-micprojects.cloudfunctions.net/ai-code-explainer --data '{"code":"    history = model.fit(X_train, y_train, epochs=100, batch_size=32, validation_split=0.1,\ncallbacks=[keras.callbacks.EarlyStopping(monitor='val_loss', patience=3, mode='min')], shuffle=False)\nprint(model.evaluate(X_test, y_test))"}'```
