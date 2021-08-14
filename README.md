[<img alt="Ponicode Logo_product_hunt" target="_blank" src="https://uploads-ssl.webflow.com/5f85a5ab7da846bd78f988af/5fbbd27b9e854653a40f579f_Couv-readme-1024x416.png" width="100%"/>](https://www.producthunt.com/posts/ponicode-dogstring)

# 🦄 Magically add docstrings to your undocumented code

This Github Action generates docstrings for your **Python** functions with the Ponicode AI engine (this action is currently in beta version)

```yaml
- uses: ponicode/dogstring-action@master
  with:
    repo_path: ./
    auth_token: ${{ secrets.PONICODE_TOKEN }}
    all_repo: False
```

Once the docstrings are written, use the [create pull request action](https://github.com/peter-evans/create-pull-request) to see the results in the branch of your choice

```yaml
- name: Create Pull Request
  uses: peter-evans/create-pull-request@v3
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    commit-message: "[ponicode-pull-request] Ponicode found docstrings to write!"
    branch: ponicode-dogstring
    title: "[Ponicode] Docstrings created"
    body: |
      Ponicode report
      - Ponicode found docstrings to write
```

## Terms of use

When you use this action, Ponicode will send the content of all the Python files of your project to the Ponicode API in order to provide you with relevant documentation. Some of your code might be stored to improve our prediction models, but it will never be shared with a third-party.

# How to setup

## Create a yaml workflow file in your project

Go to the root of your project, and create the path to your workflows directory:

```
mkdir -p .github/workflows
```

Now create a YAML file in this directory, name it `ponicode.yml` and copy one of the following example in it! <br />

Here is an example of what to put in your `.github/workflows/ponicode.yml` file to trigger the action.

```yaml
name: Ponicode DogString

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Get paths
        run: |
          git show --pretty="" --name-only ${{ github.sha }} > PATHS_TO_CHANGED_FILES.txt
      - uses: ponicode/docstrings-action@master
        with:
          repo_path: ./
          auth_token: ${{ secrets.PONICODE_TOKEN }}
          all_repo: True
        # Creates pull request with all changes in file
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          commit-message: "[ponicode-pull-request] Ponicode wrote new docstrings!"
          branch: ponicode-docstring
          title: "[Ponicode] Docstrings created"
          body: |
            ## ⭐️ Ponicode report ⭐️
            Ponicode found **undocumented functions** in your code, and auto-generated docstrings for you.
            </br>
            ### 🦄 We'd love to hear your feedback!🦄 
            Send us an email at <feedback@ponicode.com>, open an issue on our Action, or join us on the [Ponicode Community Slack](https://ponicode-community.slack.com/join/shared_invite/zt-fiq4fhkg-DE~a_FkJ7xtiZxW7efyA4Q#/).
            Visit **[ponicode.com](https://ponicode.com)** to find out more about what we do.
            </br>
            <img alt="Ponicode Logo" src="https://avatars0.githubusercontent.com/u/49948625?s=200&v=4=200zx" width="100"/>
```

**This YAML file writes docstrings on your undocumented Python functions everytime you push on main, commits them to the branch `ponicode-dogstring` and makes a Pull Request for you.**

## Step 2: Add your Ponicode token to github secrets

To get a Ponicode token follow these steps:

- Connect to your ponicode member page at https://app.ponicode.com/actions
- Copy your ponicode token

To add the Ponicode token to your github secrets follow these steps:

- Open your project on Github
- Click on `Settings`
- Click on `Secrets`
- Click on `New Secret`
- Name: `PONICODE_TOKEN`, Value: (Paste your token from VS code)

That's it! Once this is done, the action will be triggered on every push.

# Ponicode Action inputs

| Name              | Description                                                                                                                                                  | Required | Default  |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- | -------- |
| `repo_path`       | The relative path in your repo to the files you want Ponicode to test. By default, Ponicode tests your whole repo.                                           | true     | `./`     |
| `auth_token`      | String. No default value. You need to add your authentication ponicode token at https://app.ponicode.com/actions                                             | true     | ` `      |
| `all_repo`        | Boolean. By default, the value is False. Choose if you want to write docstrings only on the files you just commited (False) or on all your repository (True) | true     | ` False` |
| `enable_template` | Boolean. By default, the value is true. Choose if you want to docstrings to include params and templates                                                     | false    | ` True`  |

# Use cases:

### Trigger Ponicode action on push on `custom` branch.

```yaml
name: Ponicode DogString

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [custom]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Get paths
        run: |
          git show --pretty="" --name-only ${{ github.sha }} > PATHS_TO_CHANGED_FILES.txt
      - uses: ponicode/dogstring-action@master
        with:
          repo_path: ./
          auth_token: ${{ secrets.PONICODE_TOKEN }}
          all_repo: True
        # Creates pull request with all changes in file
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          commit-message: "[ponicode-pull-request] Ponicode wrote new docstrings!"
          branch: ponicode-docstring
          title: "[Ponicode] Docstrings created"
          body: |
            ## ⭐️ Ponicode report ⭐️
            Ponicode found **undocumented functions** in your code, and auto-generated docstrings for you.
            </br>
            ### 🦄 We'd love to hear your feedback!🦄 
            Send us an email at <feedback@ponicode.com>, open an issue on our Action, or join us on the [Ponicode Community Slack](https://ponicode-community.slack.com/join/shared_invite/zt-fiq4fhkg-DE~a_FkJ7xtiZxW7efyA4Q#/).
            Visit **[ponicode.com](https://ponicode.com)** to find out more about what we do.
            </br>
            <img alt="Ponicode Logo" src="https://avatars0.githubusercontent.com/u/49948625?s=200&v=4=200zx" width="100"/>
```

### Trigger Ponicode action when you push on `main` or when you make a pull request on `custom` branch

```yaml
name: Ponicode DogString

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [main]
  pull_request:
    branches: [custom]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Get paths
        run: |
          git show --pretty="" --name-only ${{ github.sha }} > PATHS_TO_CHANGED_FILES.txt
      - uses: ponicode/dogstring-action@master
        with:
          repo_path: ./
          auth_token: ${{ secrets.PONICODE_TOKEN }}
          all_repo: True
        # Creates pull request with all changes in file
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          commit-message: "[ponicode-pull-request] Ponicode wrote new docstrings!"
          branch: ponicode-docstring
          title: "[Ponicode] Docstrings created"
          body: |
            ## ⭐️ Ponicode report ⭐️
            Ponicode found **undocumented functions** in your code, and auto-generated docstrings for you.
            </br>
            ### 🦄 We'd love to hear your feedback!🦄 
            Send us an email at <feedback@ponicode.com>, open an issue on our Action, or join us on the [Ponicode Community Slack](https://ponicode-community.slack.com/join/shared_invite/zt-fiq4fhkg-DE~a_FkJ7xtiZxW7efyA4Q#/).
            Visit **[ponicode.com](https://www.google.com)** to find out more about what we do.
            </br>
            <img alt="Ponicode Logo" src="https://avatars0.githubusercontent.com/u/49948625?s=200&v=4=200zx" width="100"/>
```

### Trigger Ponicode action only on your last committed files (all_repo = False) when you push on `main`

```yaml
name: Ponicode DogString

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Get paths
        run: |
          git show --pretty="" --name-only ${{ github.sha }} > PATHS_TO_CHANGED_FILES.txt
      - uses: ponicode/dogstring-action@master
        with:
          repo_path: ./
          auth_token: ${{ secrets.PONICODE_TOKEN }}
          all_repo: False
        # Creates pull request with all changes in file
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          commit-message: "[ponicode-pull-request] Ponicode wrote new docstrings!"
          branch: ponicode-docstring
          title: "[Ponicode] Docstrings created"
          body: |
            ## ⭐️ Ponicode report ⭐️
            Ponicode found **undocumented functions** in your code, and auto-generated docstrings for you.
            </br>
            ### 🦄 We'd love to hear your feedback!🦄 
            Send us an email at <feedback@ponicode.com>, open an issue on our Action, or join us on the [Ponicode Community Slack](https://ponicode-community.slack.com/join/shared_invite/zt-fiq4fhkg-DE~a_FkJ7xtiZxW7efyA4Q#/).
            Visit **[ponicode.com](https://www.google.com)** to find out more about what we do.
            </br>
            <img alt="Ponicode Logo" src="https://avatars0.githubusercontent.com/u/49948625?s=200&v=4=200zx" width="100"/>
```

### Examples of generated docstrings

```python
def add(a, b):
    """
    Add two arguments.
    """
    return a + b

def reverse_words(input_str):
    """
    Reverse string.
    """
    return " ".join(input_str.split()[::-1])

def determinant(matrix):
    """
    Return the determines of a matrix.
    """
    if len(matrix) == 1:
        return matrix[0][0]

    return sum(
        x * determinant(minor(matrix, 0, i)) * (-1) ** i
        for i, x in enumerate(matrix[0])
    )

```

## Contact us

We would love to hear your feedback! Tell us what you loved and what you want us to improve about this action at feedback@ponicode.com, or feel free to open a Github Issue.<br />
We also have a [Slack community channel](https://ponicode-community.slack.com/join/shared_invite/zt-fiq4fhkg-DE~a_FkJ7xtiZxW7efyA4Q#/), where people can ask for help if they encounter problems with our products and where we keep you informed about our latest releases.<br />
If you want to know more about Ponicode and the different services we propose, check out our website [www.ponicode.com](https://ponicode.com)! <br /> <br/>
<img alt="Ponicode Logo" src="https://avatars0.githubusercontent.com/u/49948625?s=200&v=4=200zx" width="100"/>
