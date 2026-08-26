
#  INTRO TO GITHUB ACTIONS


## LEARNING GOALS

In this workshop, you will learn about automating your DevOps workflow by running GitHub actions, using "Github Context Variables," and passing secrets using GitHub's secret's manager.



## 1. Check that you have a workshop repo set up.

For this workshop, we created a repo for you, `https://github.ncsu.edu/CSC-519/CSC-519-WS-3-<YOUR-UNITY-ID>`

If you can't find your repo, please notify the teaching staff.


## 2. Create your first GitHub Action

GitHub provides [useful and detailed documentation of GitHub actions](https://docs.github.com/en/enterprise-cloud@latest/actions), especially [the Quickstart](https://docs.github.com/en/enterprise-cloud@latest/actions/quickstart) guide.  Depending on your learning style, you might also seek resources (videos?) from other sources.

Your repo will be empty and you'll need to creat a folder called `.github` and within this folder there is a subfolder called `workflows`.    By convention, this folder is where GitHub expects workflows to be stored.  Whenever you see a repo with an `.github/workflows` folder that is not empty, you know that this repo is using some kind of GitHub actions.

Examples of large companies GitHub workflows:

| OWNER | REPO | WORKFLOW | 
| ----- | ---- | -------- |
| Meta (Facebook)  | React | Linting [https://github.com/facebook/react/blob/main/.github/workflows/shared_lint.yml](https://github.com/facebook/react/blob/main/.github/workflows/shared_lint.yml) |
| Microsoft    | VSCode      | All PRs on Main & Release [https://github.com/microsoft/vscode/blob/main/.github/workflows/pr.yml](https://github.com/microsoft/vscode/blob/main/.github/workflows/pr.yml)     |
| Disney | Terraform | Cloud Logging [https://github.com/disney/terraform-aws-kinesis-firehose-splunk/blob/master/.github/workflows/tfsec.yml](https://github.com/disney/terraform-aws-kinesis-firehose-splunk/blob/master/.github/workflows/tfsec.yml) | 
| Amazon Web Services | aws-cli | Identify Stale Issues [https://github.com/aws/aws-cli/blob/develop/.github/workflows/stale_issue.yml](https://github.com/aws/aws-cli/blob/develop/.github/workflows/stale_issue.yml) | 

In the workshop form, there are some questions about these "large company" workflows.  You can either answer those questions now or come back to them later.

### Workflows are encoded in YAML 

"Workflows", when run, become "actions."  "GitHub Actions" is the underlying automation platform, while 'workflows' are [YAML files](https://en.wikipedia.org/wiki/YAML) and must have either a `.yml` or `.yaml` suffix.  YAML files are a kind of structured text format that specifies what the workflow does. 

YAML files have a 1-to-1 correspondence to [JSON files](https://en.wikipedia.org/wiki/JSON), which means you can convert from one to the other and back again, as can be shown in this [YAML Playground](https://eemeli.org/yaml-playground/) (it will be empty when you open it). Find a workflow (YAML file) from the list above.  Copy the contents of a YAML workflow into the playground and you'll see how it is immediately transformable into JSON.

Your workshop repo is new. 

In a new directory  `.github/workflows,` create a new file, `hello-world.yaml`.   

Edit the file `hello-world.yaml` and add the following:

```
name: hello-world
on: push
jobs:
  this-is-a-job-name:
    runs-on: self-hosted
    steps:
      - run: exit 0
```

Let's look at each line of this workflow individually:

The `name:` keyword is [technically optional](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#name), but [giving good descriptive names is important](https://dl.acm.org/doi/abs/10.1145/3092703.3092727).  You can leave the name as `hello-world` or change it if you like.

The `on:` keyword is [required and **critical** to understanding workflows](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#on).   The reason `on` is so important is because GitHub Actions are a kind of [Event-Driven architecture](https://en.wikipedia.org/wiki/Event-driven_architecture), a paradigm where you execute code in reponse to Events. 

In our code, we have `on: push`, which specifies that this workflow will run in response to `push` events in this repository.   In GitHub Actions, "events" are the foundation of workflows because they are what specify the conditions when an action should run. There are [many events that occur during GitFlow style development](https://docs.github.com/en/enterprise-cloud@latest/actions/using-workflows/events-that-trigger-workflows#pull_request) that can trigger a workflow.

Pick an event from [this list of GitHub events](https://docs.github.com/en/enterprise-cloud@latest/actions/using-workflows/events-that-trigger-workflows) and think about _what kind_ of actions you might want to take in response to this event and _why_ you might want to take that action.   Reflect on this in the Workshop's Google Form.


In this section of our workflow:
```
jobs:
  this-is-a-job-name:
    runs-on: self-hosted
    ...
```
     

The `jobs:` keyword is where we can specify 1 or more tasks that this action should take (this example has only 1 job), and we've provided a name for the this job, `this-is-a-job-name`, which is required.

The `runs-on:` keyword is where we specify what compute is going to handle our job. GitHub.com hosts their own headless VMs, but we'll be running on a machine located somewhere on premises.

```
...
    steps:
      - run: exit 0
```

Lastly, the `steps:` keyword is where was can enumerate a sequence of commands, and `- run:` is the keyword prefex for shell commands!  The shell command `exit 0` is a toy command that returns the venerable "unix 0 return code" ([RedHat link which discusses unix return codes and their uses](https://www.redhat.com/sysadmin/linux-shell-command-exit-codes)).

By running the command `exit 0`, we are forcing this action to succeed.

Change `exit 0` to any return code that does *not* mean success (see Redhat link above).

By committing this change, you should be able to see the results in the "Actions" tab.

## 3. Viewing the results of an action.

Navigate to the "Action" tab and you should see a list of "Workflow runs", including the most recent unsuccessful one.  On the Actions tab, D=drill down on the results of your workflow by clicking on the workflow name, then click on the job name (in this case, it should be "this-is-a-job-name" unless you changed it), then you can click on the output of the `run:` command. Once you've found this, copy the URL (should be something like `https://github.ncsu.edu/CSC-519/CSC-519-WS-3-<YOUR-UNITY-ID>/actions/runs/57163/jobs/117676`) and save it for the form.


## 4. Setting up a "Repository Secret"

In a previous workshop, we used an environment variable to pass secrets into a container.   For this workshop, we're going to use a "secrets manager" tool provided by GitHub.

On this repo, go to `Settings > Secrets and Variables` and click the "New repository secret" button.  Name your secret `NCSU_GITHUB_TOKEN` and for the "secret" part either create a new PAT token or use the one from a previous workshop.  The "secrets" management tool stores your token (encrypted) in a way that we can use it with our workflow without having to encode the token in the workflow.  For more instructions [see this page](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions).

You can test that your PAT still works by using (a unix-based) command line REST API command.  
First, you declare your PAT as a local variable inside your terminal

```
export NCSU_GITHUB_TOKEN=YOUR_PAT_TOKEN
```

Now that this variable is declared, the following command tests your GitHub authentication.

```
curl --request GET \
--url "https://github.ncsu.edu/api/v3" \
--header "Authorization: Bearer $NCSU_GITHUB_TOKEN"
```

When successful, the command above should return a slew of URLs. When unsuccessful, it will return "Bad Credentials" (if the PAT has expired or not copied correctly, for example).


## 5. Refering to `github` variables in workflows: "Contexts"

Within a workflow, you can access variables that store relevant information, like your GitHub username or the full name of the current repo.  GitHub refers to these variables collectively as the "`github` context."  As described in [GitHub's documentation on the "context"](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context).

For example, if we were working on a team and wanted to personalize a workflow to track our user id  (`github.actor`, should be the same as your UNITY ID), we can do the following:

```
name: hello-world
run-name: ${{ github.actor }} is learning
on: push
jobs:
  this-is-a-job-name:
    runs-on: self-hosted
    steps:
      - run: exit 0
```

In this workflow, anything inside the `${{...}}` is *accessing the context*.  Inside these `${{...}}` statements, we can add [*expressions*](https://en.wikipedia.org/wiki/Expression_(computer_science)) (like `if` statements) that will be evaluated (a.k.a executed) when the workflow is run.

So when this workflow runs, the parser/interpreter that runs it translates ``${{ github.actor }}`` in your username (which is also your UNITY ID).


## 6. Use "Github context" and secrets a workflow

__THIS SECTION AND ITS DELIVERABLE ARE INDIVIDUAL WORK.__

Your workflow should:

* Be event driven. Your workflow should run when a pull request is opened *or* reopened.
* Use `- run:` to run a `curl ...` command that lists the branches on your workshop repository *without* hard-coding your username, token, or repository name.  Running this `curl` command here is for demonstration purposes only.  The purpose of this is to demonstrate that you can pass "github context variables" and your secret through a workflow.
* You will need to figure out the correct ["github context variables"](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context) for your `run:` command.
* From the Github "Actions" tab in your workshop repo, drill down to the output of your successful `curl` command.  You'll need to click on the name of your workflow, then on the job name, then expand the individual `run` command results to see the command output.   Note and copy the URL, it should be something like `https://github.ncsu.edu/CSC-519/CSC-519-WS-3-<YOUR-UNITY-ID>/actions/runs/57163/jobs/117676` and you'll need it for the form.
* Also, Do not check in a token. Never ever.
* For this workshop only, committing to `main` is OK.


## 7. Stretch Goal (ungraded)

Find another way for your action runner to read data from your workshop repository without configuring a secret in your repo and without making the repo public.


Due 23:59 AOE September 9th, Form submitted and Repo synched 

Complete the questions here:
[Google Form file upload](https://forms.gle/pbUxjRHtQ5iX3cQt8)


## That's it :)  Good job.

## Evaluation

This workshop is worth 3 points.

You'll be graded on the following rubric:

| ITEM | POINTS |
|--|--|
| Questions & URL | 2 |
| Program fulfills requirements | 1 |
| Checked in a token | -2 |

