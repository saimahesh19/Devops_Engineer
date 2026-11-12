# Chef Foundations: A Beginner-Friendly Study Guide

**Purpose:** Explain the supplied `chef-notes.txt` in clear English, with file locations, small examples, expected results, and ASCII flow diagrams.

**Learning status:** Introductory theory and illustrative examples have been covered. The examples have not been executed or validated in your environment. This guide is not evidence of hands-on completion or migration readiness.

**Scope:** The guide covers all substantive topics in the supplied notes. Repeated daily recaps are consolidated rather than copied repeatedly. Historical "pending" lists are retained as a learning timeline and reconciled in the final checklist. The source's Day 1 section stops during the run-list explanation; brief Organization and Ohai reminders are included from our earlier lessons and identified as such. Role-versus-environment override comparison and encrypted-item reading are also included from our follow-up lessons, because the source's earlier pending lists predate those explanations.

**How to use this guide:** Read one or two sections at a time. Understand the purpose and file location before studying syntax. Commands are examples to recognize, not instructions to run against company systems.

---

## Contents

1. [Why Chef exists and what desired state means](#1-why-chef-exists-and-what-desired-state-means)
2. [Architecture and where work happens](#2-architecture-and-where-work-happens)
3. [Cookbooks, recipes, resources, and actions](#3-cookbooks-recipes-resources-and-actions)
4. [Repository and cookbook file structure](#4-repository-and-cookbook-file-structure)
5. [Metadata and a first Nginx recipe](#5-metadata-and-a-first-nginx-recipe)
6. [Attributes](#6-attributes)
7. [Templates and ERB](#7-templates-and-erb)
8. [Directories, files, and cookbook files](#8-directories-files-and-cookbook-files)
9. [Idempotency](#9-idempotency)
10. [Execute and guards](#10-execute-and-guards)
11. [Run-lists](#11-run-lists)
12. [Cookbook dependencies and include_recipe](#12-cookbook-dependencies-and-include_recipe)
13. [Libraries](#13-libraries)
14. [Bash and script idempotency](#14-bash-and-script-idempotency)
15. [Users and groups](#15-users-and-groups)
16. [PowerShell scripts](#16-powershell-scripts)
17. [A complete small cookbook](#17-a-complete-small-cookbook)
18. [Roles and environments](#18-roles-and-environments)
19. [Compile versus converge](#19-compile-versus-converge)
20. [Attribute precedence and overrides](#20-attribute-precedence-and-overrides)
21. [Data bags](#21-data-bags)
22. [Encrypted data bags](#22-encrypted-data-bags)
23. [Chef CLI, knife, and chef-client](#23-chef-cli-knife-and-chef-client)
24. [Logging and debugging](#24-logging-and-debugging)
25. [Test Kitchen and kitchen.yml](#25-test-kitchen-and-kitchenyml)
26. [Verification with InSpec](#26-verification-with-inspec)
27. [End-to-end flow and common confusions](#27-end-to-end-flow-and-common-confusions)
28. [Coverage, remaining work, and a practice plan](#28-coverage-remaining-work-and-a-practice-plan)
29. [Quick-reference glossary](#29-quick-reference-glossary)
30. [Source and scope notes](#30-source-and-scope-notes)

---

## 1. Why Chef exists and what desired state means

Imagine you manage ten Linux servers. Every server needs Nginx installed, a correct configuration file, and the Nginx service running.

You could sign in to every server and do the work manually. However, repeating the work is slow and can produce differences between servers. Chef lets you describe the required configuration in code.

**Chef is a configuration-management tool: it uses code to manage how machines are configured.**

### Desired state

Desired state means the final condition you want, not a history of commands you want repeated.

Example desired state:

> Nginx must be installed, enabled at boot, and running.

```text
Desired state in code
        |
        v
Chef Client checks the node
        |
        +--> State differs ------> Make required changes
        |
        +--> State already right -> No configuration change needed
```

Chef Client performs this work when it runs. Do not assume Chef continuously watches every change every second.

**Remember:** You describe what should be true; suitable Chef resources check and manage that state.

## 2. Architecture and where work happens

The examples use the traditional Chef Server model. Local-mode and other workflows exist, but are outside this beginner explanation.

| Component | Simple meaning | Where it fits |
|---|---|---|
| Workstation | Where you write and test automation code | Developer's machine |
| Chef Server | Central hub for cookbooks and configuration data | Server-side service |
| Node | A machine managed by Chef | Target Linux or Windows machine |
| Chef Client | Software that applies configuration | Runs on the node |

### End-to-end architecture

```text
WORKSTATION                  CHEF SERVER                    NODE
+------------------+         +------------------+          +------------------+
| Write cookbooks  | upload  | Store cookbooks  |          | Chef Client      |
| Test code        | ------> | Store node data  | <------  | requests config  |
+------------------+         +------------------+          +------------------+
                                      |                            |
                                      +--- return configuration -->|
                                                                   v
                                                        Check and configure
                                                        packages/files/services
```

The node initiates configuration retrieval in this model. Chef Server stores and supplies configuration; Chef Client performs the actual configuration work on the node.

### Organization reminder

*Supplement from our earlier lesson; the attached Day 1 section ends before this reminder.*

An organization is a separate namespace and access boundary inside Chef Server. It has its own cookbooks, nodes, roles, environments, and permissions.

```text
Chef Server
+------------------------------------------------+
| Organization: team-a                           |
|   Nodes + cookbooks + roles + environments      |
|                                                |
| Organization: team-b                           |
|   Separate nodes + cookbooks + configuration    |
+------------------------------------------------+
```

An organization is not a folder inside your cookbook. Two organizations can have cookbooks with the same name without those being the same server-side object. These are imaginary examples, not a description of your company's actual setup.

### Ohai reminder

*Supplement from our earlier lesson.*

Ohai collects machine information such as platform, hostname, CPU, memory, and network details. Chef Client normally runs Ohai at the start of a run. The collected values are called automatic attributes.

```ruby
node['platform']
node['hostname']
```

These expressions read values. Writing them alone does not print the values or create files.

**Difference:** A cookbook attribute can express what you want; an Ohai attribute describes a detected machine fact.

## 3. Cookbooks, recipes, resources, and actions

Think of these as levels of organization, not four separate tools.

```text
Cookbook: web_setup
        |
        +--> Recipe: default.rb
                    |
                    +--> package resource: manage Nginx installation
                    +--> service resource: manage Nginx service
```

- **Cookbook:** A package of related automation code and supporting files.
- **Recipe:** A file containing configuration instructions, usually resource declarations and Ruby logic.
- **Resource:** A declaration that manages a particular item, such as a package, file, service, or user.
- **Action:** What the resource should do, such as install, create, start, or enable.

### Installed, started, and enabled are different

| Term | Meaning |
|---|---|
| Installed | Software is present on the machine |
| Started | Its service is running now |
| Enabled | Its service is configured to start at boot |

Installing software does not automatically mean its service is running. Enabling a service is not the same as starting it immediately.

### Reading a resource block

```ruby
package 'nginx' do
  action :install
end
```

- `package`: resource type.
- `'nginx'`: resource name, used here as the package name.
- `action :install`: ensure the package is installed.
- `do ... end`: boundaries of the resource block.

## 4. Repository and cookbook file structure

A **repository** is the larger source-code workspace. A **cookbook** is one package inside it.

This is an illustrative roles/environments-based repository, not a mandatory structure for every Chef workflow.

```text
chef-repo/                         WORKSTATION SOURCE REPOSITORY
|-- cookbooks/
|   |-- base_setup/
|   |   |-- metadata.rb
|   |   `-- recipes/
|   |       `-- default.rb
|   `-- web_setup/
|       |-- metadata.rb
|       |-- kitchen.yml
|       |-- recipes/
|       |   |-- default.rb
|       |   `-- windows.rb
|       |-- attributes/
|       |   `-- default.rb
|       |-- templates/
|       |   `-- default/
|       |       `-- welcome.erb
|       |-- files/
|       |   `-- default/
|       |       `-- welcome.txt
|       |-- libraries/
|       |   `-- helpers.rb
|       `-- test/
|           `-- integration/
|               `-- default/
|                   `-- default_test.rb
|-- roles/
|   `-- webserver.rb
|-- environments/
|   |-- dev.rb
|   `-- prod.rb
`-- data_bags/
    `-- apps/
        `-- website.json
```

| Location | Purpose |
|---|---|
| `metadata.rb` | Cookbook identity, version, and dependencies |
| `recipes/` | Configuration instructions |
| `attributes/` | Cookbook configuration values |
| `templates/` | ERB patterns used to generate files |
| `files/` | Source files copied without template rendering |
| `libraries/` | Reusable Ruby helper code |
| `kitchen.yml` | Test Kitchen configuration |
| `test/integration/` | Verification tests |
| Repository `roles/` | Role definitions |
| Repository `environments/` | Environment definitions |
| Repository `data_bags/` | Data bag item source definitions |

Not every cookbook needs every directory. A small cookbook can contain only metadata and a recipe.

**Important:** `attributes/default.rb` and `recipes/default.rb` have the same filename but different purposes and locations. `default/` inside `files/` or `templates/` is also not the Chef Server `_default` environment.

## 5. Metadata and a first Nginx recipe

### Cookbook details

**File:** `web_setup/metadata.rb`

```ruby
name 'web_setup'
version '0.1.0'
```

`name` identifies the cookbook. `version` identifies this version of the cookbook code. It is not the Nginx software version.

### Actual configuration

**File:** `web_setup/recipes/default.rb`

```ruby
package 'nginx' do
  action :install
end

service 'nginx' do
  action [:enable, :start]
end
```

The first resource manages package installation. The second manages service startup at boot and its running state.

```text
Recipe selected
      |
      v
Ensure Nginx package is installed
      |
      v
Ensure Nginx service is enabled and started
```

**Assumptions:** The node has a suitable OS, package repository, permissions, and service-management support. This example is not guaranteed to run unchanged on every platform.

## 6. Attributes

An attribute is a configuration value. Separating values from resource instructions makes code easier to adjust and reuse.

### Define the value

**File:** `web_setup/attributes/default.rb`

```ruby
default['web_setup']['package_name'] = 'nginx'
```

- `default`: attribute precedence level used for this setting.
- `web_setup`: a grouping key for this cookbook's settings.
- `package_name`: setting name.
- `nginx`: setting value.

### Read the final value

**File:** `web_setup/recipes/default.rb`

```ruby
package node['web_setup']['package_name'] do
  action :install
end
```

`node[...]` reads the resolved attribute value. Assuming no competing value overrides it, the package name is `nginx`.

```text
attributes/default.rb                  recipes/default.rb
package_name = nginx  --------------->  Read final attribute
                                                |
                                                v
                                       Manage package installation
```

**Remember:** Attribute = which value? Recipe = what should happen using that value?

## 7. Templates and ERB

A template is a text pattern containing placeholders. Chef renders it with values to produce a final file. ERB means Embedded Ruby.

### Step 1: Define a value

**File:** `web_setup/attributes/default.rb`

```ruby
default['web_setup']['server_label'] = 'Dev'
```

### Step 2: Prepare the template

**File:** `web_setup/templates/default/welcome.erb`

```erb
Welcome to <%= @server_label %> server!
```

`<%= ... %>` inserts the expression's result into the output. `@server_label` is the variable passed to this template.

### Step 3: Connect the value and template

**File:** `web_setup/recipes/default.rb`

```ruby
template '/tmp/chef_welcome.txt' do
  source 'welcome.erb'
  variables(server_label: node['web_setup']['server_label'])
end
```

- Resource path: destination file on the node.
- `source`: template inside the cookbook.
- `variables`: values supplied to the template.

```text
Attribute: Dev ----+
                  |
Template:         v
Welcome to [value] server!
                  |
                  v
Node: /tmp/chef_welcome.txt
Welcome to Dev server!
```

The template source remains part of the cookbook. The generated output file exists on the node. This example is a message file, not a complete Nginx configuration.

## 8. Directories, files, and cookbook files

This distinction is important: **the cookbook contains instructions and source material; the node contains the managed result.**

### A. `directory`: manage a folder

**File:** `web_setup/recipes/default.rb`

```ruby
directory '/tmp/myapp' do
  action :create
end
```

This manages `/tmp/myapp` on the node. It is not creating a new cookbook folder on your workstation.

### B. `file`: provide content in the recipe

```ruby
directory '/tmp/myapp' do
  action :create
end

file '/tmp/myapp/message.txt' do
  content 'Hello Mahesh!'
  action :create
end
```

The text is directly in the recipe. No separate source-content file is required. If the file is missing, Chef creates it; if its content differs, Chef updates it to match the declaration.

The parent directory must exist. The `file` resource does not automatically create missing parent directories.

### C. `cookbook_file`: copy a separate source file

**Source file:** `web_setup/files/default/message.txt`

```text
Hello Mahesh!
```

**Recipe:** `web_setup/recipes/default.rb`

```ruby
directory '/tmp/myapp' do
  action :create
end

cookbook_file '/tmp/myapp/message.txt' do
  source 'message.txt'
  action :create
end
```

```text
COOKBOOK SOURCE                          TARGET NODE
files/default/message.txt  ----------->  /tmp/myapp/message.txt
Contains: Hello Mahesh!                  Contains: Hello Mahesh!
```

"Separate source" means a real content file separate from the recipe, not an unexplained external server.

Creating `files/` inside a cookbook does not automatically create a `files/` directory on the node. The resource destination path controls where the managed file goes.

### Choose the right resource

| Requirement | Resource |
|---|---|
| Ensure a folder exists | `directory` |
| Put short content directly in a file | `file` with `content` |
| Copy a prepared cookbook source file | `cookbook_file` with `source` |
| Generate a file using placeholders | `template` |

`file` and `cookbook_file` are alternatives for many requirements. Do not manage the same destination with both without a deliberate reason.

## 9. Idempotency

Idempotency means repeated application does not introduce unwanted extra changes once the desired state is satisfied.

For example:

```ruby
package 'nginx' do
  action :install
end
```

```text
Chef run
   |
   v
Is the required package installed?
   |
   +-- No --> Install it
   |
   +-- Yes -> No installation change needed
```

- First run: install if missing.
- Later run: do not reinstall unnecessarily when the required state is satisfied.
- If someone removes it: a later run can restore it.

**Analogy:** "The door must be locked." If unlocked, lock it. If already locked, do not perform unnecessary work.

Idempotency does not mean Chef never runs again. It means repeated application should not cause unwanted side effects. Shell scripts need particular care because their commands may repeat work every time.

## 10. Execute and guards

### Execute a command

**File:** `web_setup/recipes/default.rb`

```ruby
execute 'create_marker' do
  command 'touch /tmp/setup_done'
end
```

`create_marker` is a descriptive resource name. The `command` property contains the shell command.

`touch` creates a missing file, but normally updates timestamps if the file already exists. It is not simply "do nothing when the file exists."

### Guard meanings

| Guard | Meaning when its condition is true |
|---|---|
| `not_if` | Skip the resource action |
| `only_if` | Allow the resource action |

For shell-string guards, success exit status `0` means the condition is true. A nonzero status means false. Ruby-block guards use a true/false result.

### Marker-file example

```ruby
execute 'create_marker' do
  command 'touch /tmp/setup_done'
  not_if 'test -f /tmp/setup_done'
end
```

```text
Before executing the action
            |
            v
Does /tmp/setup_done exist as a regular file?
            |
            +-- Yes --> not_if is true --> Skip touch
            |
            +-- No ---> not_if is false -> Run touch
```

### Can `not_if` appear first?

For this simple example, yes:

```ruby
execute 'create_marker' do
  not_if 'test -f /tmp/setup_done'
  command 'touch /tmp/setup_done'
end
```

The `command` property defines what should run. Reading that property line does not immediately execute the command. Chef evaluates the guard before performing the resource action.

**Do not generalize this to "all recipe line order is irrelevant."** Resource order and Ruby execution can matter.

### Marker limitations

A marker guard only checks the condition you wrote. The presence of `/tmp/setup_done` does not prove an application is healthy or every setup step succeeded.

## 11. Run-lists

A run-list selects recipes and/or roles for a node and defines their order. It is not a compulsory `runlist.rb` file inside a cookbook.

Example cookbook:

```text
web_setup/
`-- recipes/
    |-- install.rb
    |-- configure.rb
    `-- start.rb
```

Illustrative node data:

```json
{
  "name": "server-01",
  "run_list": [
    "recipe[web_setup::install]",
    "recipe[web_setup::configure]",
    "recipe[web_setup::start]"
  ]
}
```

`recipe[web_setup::install]` refers to `web_setup/recipes/install.rb`.

```text
Node: server-01
      |
      v
Run-list selects recipes
      |
      +--> web_setup::install
      +--> web_setup::configure
      `--> web_setup::start
```

In the traditional Chef Server model, the run-list is stored in the node object on Chef Server. The JSON above illustrates node data; it is not a file you must create in `metadata.rb` or the cookbook.

Different nodes can have different run-lists. Splitting a cookbook into these three recipes is an example, not a requirement. Detailed action timing can also be affected by notifications and other advanced features, which are outside this guide.

## 12. Cookbook dependencies and include_recipe

Suppose `web_setup` needs another cookbook named `base_setup`. These are separate sibling cookbooks, not nested cookbooks.

### Declare the dependency

**File:** `cookbooks/web_setup/metadata.rb`

```ruby
name 'web_setup'
version '0.1.0'
depends 'base_setup'
```

This declares that the other cookbook is required. It does not, by itself, execute its default recipe.

### Include a recipe

**File:** `cookbooks/web_setup/recipes/default.rb`

```ruby
include_recipe 'base_setup::default'

package 'nginx' do
  action :install
end
```

The included recipe's resources are inserted at the inclusion point in their order.

```text
metadata.rb: depends 'base_setup'
             |
             v
Declare required cookbook

recipes/default.rb: include_recipe 'base_setup::default'
             |
             v
Include that recipe's resources here
```

**Remember:** `depends` = required cookbook; `include_recipe` = include recipe instructions. Dependencies must also be available through the chosen Chef workflow; typing a dependency does not create its code.

## 13. Libraries

Libraries contain reusable Ruby helper code. They are useful when several recipes or custom resources need the same logic.

**File:** `web_setup/libraries/helpers.rb`

```ruby
module WebSetup
  def self.message
    'Welcome to our server!'
  end
end
```

- `module WebSetup`: groups the helper code under a name.
- `def self.message`: defines a method called `message` on that module.
- The text is the method's return value.

A recipe can use:

```ruby
WebSetup.message
```

This returns text. It does not automatically print it or write a file. To use the returned value in a resource:

```ruby
file '/tmp/library_message.txt' do
  content WebSetup.message
end
```

```text
libraries/helpers.rb
  reusable method
        |
        v
Recipe calls method
        |
        v
Returned text becomes file content
```

Advanced Ruby programming and extending Chef internals were not covered. For now, remember **libraries = reusable helpers**.

## 14. Bash and script idempotency

### Basic Bash resource

**File:** `web_setup/recipes/default.rb`

```ruby
bash 'show_message' do
  code 'echo "Hello Mahesh!"'
end
```

The resource executes code using Bash on the target node. Its default action is `:run`.

Chef does not automatically understand the business result of arbitrary script commands. Whether repeated execution is safe depends on the script and its guards.

### A non-idempotent example

```ruby
bash 'add_message' do
  code 'echo "Welcome" >> /tmp/messages.txt'
end
```

`>>` appends content. Repeated runs can produce:

```text
Welcome
Welcome
Welcome
```

If the requirement is "the line should appear once," this does not satisfy idempotency.

### Add a suitable guard

```ruby
bash 'add_message' do
  code 'echo "Welcome" >> /tmp/messages.txt'
  not_if 'grep -qxF "Welcome" /tmp/messages.txt'
end
```

`grep -qxF` checks for an exact full line using fixed text.

```text
Exact Welcome line already present?
                |
                +-- Yes --> Skip append
                |
                +-- No ---> Append Welcome
```

This guard prevents further additions when the line exists. It does not remove old duplicate lines. A missing file can make the check return nonzero, allowing the append to create the file; other read failures also need care in real automation.

For a simple "this entire file must contain this text" requirement, the `file` resource is usually clearer than a Bash script.

### Marker guard with Bash

```ruby
bash 'create_marker' do
  code 'touch /tmp/setup_done'
  not_if 'test -f /tmp/setup_done'
end
```

This checks only marker existence. It is not a complete application-health check.

**Remember:** Idempotency does not mean a script can never run again. It means repeated application should not introduce unwanted changes.

## 15. Users and groups

These resources manage accounts and local groups on the node, not Chef Server login accounts or organizations.

### User

**File:** `web_setup/recipes/default.rb`

```ruby
user 'appuser' do
  action :create
end
```

This creates the account if missing and manages explicitly declared properties. This minimal example does not specify a password, home-directory management, or a complete login-access policy.

### Group

Assume `appuser` already exists, or put its user resource before the group resource in the example recipe.

```ruby
group 'appteam' do
  members ['appuser']
  append true
  action :create
end
```

- `members`: users to include.
- `append true`: add specified members without replacing the existing membership list.
- `action :create`: create or manage the group.

With `append false`, membership can be reset to the declared members. Do not omit this distinction when modifying real groups.

```text
Target node
+---------------------------+
| Account: appuser          |
|          |                |
|          v                |
| Local group: appteam      |
+---------------------------+
```

## 16. PowerShell scripts

Use `powershell_script` for a Windows-node script example.

**File:** `web_setup/recipes/windows.rb`

```ruby
powershell_script 'show_message' do
  code 'Write-Output "Hello Mahesh!"'
end
```

- Resource name: `show_message`.
- Script: the value of `code`.
- Default action: `:run`.

This is a separate Windows learning example. Do not add it blindly to a Linux recipe. Naming a file `windows.rb` does not itself prevent someone from selecting it on the wrong platform.

Like Bash, arbitrary PowerShell scripts are not automatically idempotent. Suitable script logic or guards may be required. A Windows-specific guard example was not covered.

## 17. A complete small cookbook

### Goal

Ensure `/tmp/myapp/` exists and copy a welcome file into it.

### Structure

```text
web_setup/
|-- metadata.rb
|-- recipes/
|   `-- default.rb
`-- files/
    `-- default/
        `-- welcome.txt
```

### File 1: `web_setup/metadata.rb`

```ruby
name 'web_setup'
version '0.1.0'
```

### File 2: `web_setup/files/default/welcome.txt`

```text
Welcome to our server!
```

### File 3: `web_setup/recipes/default.rb`

```ruby
directory '/tmp/myapp' do
  action :create
end

cookbook_file '/tmp/myapp/welcome.txt' do
  source 'welcome.txt'
  action :create
end
```

### Node selection

The example assumes the node selects:

```text
recipe[web_setup::default]
```

This reference belongs in node/run-list configuration, not in cookbook metadata.

### Expected result

```text
TARGET NODE
/tmp/myapp/
`-- welcome.txt
    Content: Welcome to our server!
```

```text
Cookbook metadata ---------> Identifies cookbook
Cookbook source file ------> Supplies content
Recipe -------------------> Describes folder and file
Node run-list -------------> Selects recipe
Chef Client ---------------> Checks/applies configuration
```

This small example does not require attributes, templates, libraries, or another cookbook. Do not add features simply because you learned their names.

## 18. Roles and environments

### Role: What job does this server perform?

A role can define a reusable run-list and attributes for nodes with the same function.

**File:** `chef-repo/roles/webserver.rb`

```ruby
name 'webserver'
run_list 'recipe[web_setup::default]'
```

A node can reference the role in its run-list:

```text
role[webserver]
```

Creating a local role file does not automatically upload it or assign it to all nodes.

### Environment: Which deployment stage is this node in?

Examples are `dev`, `test`, and `prod`. Environments can hold stage-specific attributes and cookbook-version constraints.

**File:** `chef-repo/environments/dev.rb`

```ruby
name 'dev'
description 'Development environment'
```

| Example node | Role | Environment |
|---|---|---|
| `dev-web-01` | webserver | dev |
| `prod-web-01` | webserver | prod |

Both nodes have the same job but different deployment stages.

```text
Repository file
      |
      v
Upload/manage definition on Chef Server
      |
      v
Associate the role/environment with a node
      |
      v
Chef Client uses the relevant configuration during a run
```

Roles and environments are outside individual cookbook directories in this repository example. An environment does not create a VM. Upload, assignment, and execution are separate operations.

Cookbook-version constraints were introduced as a capability, not taught or practiced in detail.

## 19. Compile versus converge

This distinction is about **preparing resource definitions versus acting on the node**, not preparing a real file versus copying it.

**File:** `web_setup/recipes/default.rb`

```ruby
file '/tmp/message.txt' do
  content 'Hello Mahesh!'
  action :create
end
```

### Compile

Chef evaluates recipe code and builds a resource collection in memory. For this ordinary resource declaration, it records:

```text
Type:    file
Path:    /tmp/message.txt
Content: Hello Mahesh!
Action:  create
```

The actual destination file is not created just because this declaration was read.

### Converge

Chef processes the resources and performs their actions as needed:

```text
Recipe code
    |
    v
COMPILE
Build resource definitions in memory
    |
    v
CONVERGE
Process resource actions on the node
    |
    +--> Missing file ----------> Create
    +--> Different content -----> Update
    `--> Already correct -------> No change
```

The resource collection includes more than filenames: it includes resource types, names, properties, and actions for packages, services, directories, files, and other resources.

**Important qualifications:**

- Converge does not mean a change must occur.
- Ordinary Ruby code in recipes can execute during compilation.
- Chef supports explicit compile-time resource execution and other timing features.
- This beginner explanation describes the normal resource lifecycle, not every advanced exception.

**Remember:** Compile = prepare what to manage. Converge = check and manage the node.

## 20. Attribute precedence and overrides

When several sources define the same setting, precedence determines the resolved value.

### Cookbook default

**File:** `chef-repo/cookbooks/web_setup/attributes/default.rb`

```ruby
default['web_setup']['server_label'] = 'Dev'
```

### Environment override

**File:** `chef-repo/environments/prod.rb`

```ruby
name 'prod'

override_attributes(
  'web_setup' => {
    'server_label' => 'Prod'
  }
)
```

Assuming the node belongs to `prod` and there are no other competing values:

```ruby
node['web_setup']['server_label']
```

resolves to `Prod`. An override-level attribute has higher precedence than a default-level attribute.

### Role override and comparison

*Follow-up lesson supplement: the source's earlier Day 3 pending list predates this explanation.*

**File:** `chef-repo/roles/webserver.rb`

```ruby
name 'webserver'
run_list 'recipe[web_setup::default]'

override_attributes(
  'web_setup' => {
    'server_label' => 'Web Server'
  }
)
```

If the environment also defines an override for the same key, the environment override wins over the role override.

```text
Example values, from lower to higher priority:

Cookbook default: Basic
           |
           v
Role override: Web Server
           |
           v
Environment override: Production Web Server
           |
           v
Resolved value: Production Web Server
```

The diagram assumes those are the only relevant values and the role/environment are assigned.

**Do not memorize "environment always wins."** At the default level, role defaults take precedence over environment defaults. At the override level, environment overrides take precedence over role overrides.

This guide covers the beginner comparisons, not the complete precedence table or all merging behaviour. Full precedence details remain a deeper-study topic.

## 21. Data bags

A data bag is a collection of JSON items. An item is one record. Recipes can read the data instead of hardcoding every value.

### Source structure

```text
chef-repo/
|-- cookbooks/
|   `-- web_setup/
|       `-- recipes/
|           `-- default.rb
`-- data_bags/
    `-- apps/
        `-- website.json
```

- Bag: `apps`.
- Item: `website`.
- Source definition: `website.json`.

### JSON item

**File:** `chef-repo/data_bags/apps/website.json`

```json
{
  "id": "website",
  "message": "Welcome Mahesh!"
}
```

### Read it in a recipe

**File:** `chef-repo/cookbooks/web_setup/recipes/default.rb`

```ruby
app = data_bag_item('apps', 'website')

file '/tmp/welcome.txt' do
  content app['message']
end
```

- `data_bag_item(...)`: retrieves the item.
- `app`: variable holding the retrieved data.
- `app['message']`: reads its message value.

```text
Repository JSON -- upload separately --> Chef Server item
                                                |
                                                v
                                    Recipe retrieves item
                                                |
                                                v
                                    app['message'] supplies text
                                                |
                                                v
                                    Node file: /tmp/welcome.txt
```

Expected content: `Welcome Mahesh!`

The traditional server-based example assumes the item already exists on Chef Server. Creating a local JSON file is not an upload. Upload commands were not practiced.

## 22. Encrypted data bags

An encrypted data bag item protects sensitive values using encryption and a shared secret. A plain JSON file is not encrypted just because it is in `data_bags/`.

```text
Plain data + shared secret
            |
            v
Separate encryption operation
            |
            v
Encrypted item on Chef Server
            |
            +------ matching secret available on node
                                      |
                                      v
                              Decrypt and use in recipe
```

**Analogy:** Encrypted item = locked notebook; shared secret = key.

A commonly configured node-side secret location is:

```text
/etc/chef/encrypted_data_bag_secret
```

The location can be configured differently. It is a node-side secret file, not a cookbook source file to commit alongside recipes.

### Reading an encrypted item

*Follow-up lesson supplement: the attached Day 3 notes originally marked this as pending.*

**Assumptions:** The encrypted item already exists, the matching secret is securely provisioned on the node, and the client has appropriate server access.

**File:** `web_setup/recipes/default.rb`

```ruby
secret = Chef::EncryptedDataBagItem.load_secret(
  '/etc/chef/encrypted_data_bag_secret'
)

app = Chef::EncryptedDataBagItem.load(
  'apps', 'website', secret
)
```

The first call reads the secret. The second retrieves and decrypts the item. `app` holds the decrypted data for use by the recipe.

**Security reminders:**

- Do not hardcode the shared secret in a recipe.
- Do not print decrypted passwords or secrets in logs.
- Do not commit plaintext credentials or secret-key files to source control.
- Access to the server-side item and access to the decryption secret are separate requirements.

Actual encryption commands, secure secret provisioning, and operational rotation were not covered or validated.

## 23. Chef CLI, knife, and chef-client

These tools have different jobs.

| Tool | Purpose in this learning flow |
|---|---|
| `chef generate` | Generate starter development files |
| `knife` | Interact with Chef Server objects |
| `chef-client` | Apply configuration on the node |

### Check command availability

```bash
command -v chef
command -v knife
command -v chef-client
```

A returned path means the shell can locate the command. No output can mean it is not installed or is not in `PATH`; it does not prove which cause applies.

### Generate a cookbook

Assume the current directory is `chef-repo/cookbooks/`:

```bash
chef generate cookbook web_setup
```

This generates development scaffolding. It does not install Nginx on a target node. Generated files vary with the tooling and generator configuration.

### Query Chef Server

```bash
knife node list
knife cookbook list
```

These query the configured Chef Server organization. They do not apply recipes to nodes.

`knife` needs correct server/organization configuration, credentials, and permissions. Installing the command alone does not connect it to company infrastructure.

### Important command caution

```bash
chef-client -l debug
```

This starts a configuration run with debug-level logging. It is not simply a command to view old logs. It may change the node and should only be used in an approved environment.

```text
Generate code --> Edit code --> Upload/manage separately
                                      |
                                      v
                             Assign node configuration
                                      |
                                      v
                             Chef Client applies it
```

Authentication, uploads, and assignment operations remain practical learning tasks.

## 24. Logging and debugging

### Logging: record what happened

**File:** `web_setup/recipes/default.rb`

```ruby
log 'Starting web setup' do
  level :info
end
```

This normally emits a message during converge to Chef's configured logging destination. It does not create a separate message file. Visibility depends on the configured logging level.

### Debugging: investigate why something failed

Example failure-prone recipe:

```ruby
file '/tmp/myapp/message.txt' do
  content 'Hello Mahesh!'
end
```

If the parent directory is missing, the file operation can fail. One correction is to declare the parent directory first:

```ruby
directory '/tmp/myapp' do
  action :create
end

file '/tmp/myapp/message.txt' do
  content 'Hello Mahesh!'
end
```

A missing parent is only one possible cause. Always follow the real error, not a guessed cause.

```text
Failure
   |
   v
Identify failed resource
   |
   v
Read exact error
   |
   v
Check path / prerequisites / permissions
   |
   v
Correct cause
   |
   v
Run an approved test again
```

- Logging: records events.
- Debugging: investigates problems.
- Debug logging: provides more detailed information for investigation.

Do not log passwords, shared secrets, or decrypted credentials. Reading real failure logs and troubleshooting a live lab remain pending.

## 25. Test Kitchen and kitchen.yml

Test Kitchen helps test cookbooks on configured test instances before production use. It needs a suitable driver, platform, provisioner, and verifier configuration.

### File structure

```text
web_setup/
|-- metadata.rb
|-- kitchen.yml
|-- recipes/
|   `-- default.rb
`-- test/
    `-- integration/
        `-- default/
            `-- default_test.rb
```

`kitchen.yml` is at the cookbook root in this example, not inside `recipes/`.

### Main configuration sections

| Section | Simple meaning |
|---|---|
| `driver` | How the test instance is created/managed |
| `platforms` | Which operating system/platform to test |
| `provisioner` | How configuration is applied |
| `verifier` | Which tool checks the result |
| `suites` | Which configuration and tests belong together |

### Configuration excerpt

**File:** `web_setup/kitchen.yml`

```yaml
verifier:
  name: inspec

suites:
  - name: default
    run_list:
      - recipe[web_setup::default]
    verifier:
      inspec_tests:
        - test/integration/default
```

This selects the default recipe and points InSpec at the integration tests.

**This is an excerpt, not a complete runnable lab configuration.** Driver, platform, and appropriate provisioner settings must be available for the chosen environment. No lab has been selected or configured yet.

### Converge and verify

```bash
kitchen converge
kitchen verify
```

- Converge: apply the configured Chef configuration to the test instance.
- Verify: run the configured checks.

Verify can trigger prerequisite lifecycle steps when needed. The test assertions themselves inspect the result; they do not repair it.

```text
Configured test instance
          |
          v
kitchen converge
Apply cookbook configuration
          |
          v
kitchen verify
Check expected result
          |
          +--> Pass
          `--> Fail --> Investigate recipe/test/environment
```

## 26. Verification with InSpec

### Configuration recipe

**File:** `web_setup/recipes/default.rb`

```ruby
file '/tmp/welcome.txt' do
  content 'Welcome Mahesh!'
end
```

### Verification test

**File:** `web_setup/test/integration/default/default_test.rb`

```ruby
describe file('/tmp/welcome.txt') do
  it { should exist }
  its('content') { should eq 'Welcome Mahesh!' }
end
```

The test asks:

1. Does the file exist?
2. Does its content exactly match the expected string?

Exact comparison includes details such as newlines. A copied source file with a trailing newline may not match a string without one. Keep the test aligned with the intended content.

### Two meanings of `file`

| Context | What `file` does |
|---|---|
| Chef recipe resource | Creates/manages configuration |
| InSpec resource in a test | Inspects the resulting file |

```text
recipes/default.rb
     |
     v
Create/manage node file
     |
     v
test/integration/default/default_test.rb
     |
     v
Inspect file and compare expected state
```

The test does not create or repair the file. Recipe does the work; test checks the work; Kitchen connects the workflow.

## 27. End-to-end flow and common confusions

### Whole workflow

```text
WORKSTATION
Write cookbook + values + source files + tests
             |
             v
TEST WORKFLOW
Configure Kitchen -> Converge -> Verify -> Investigate failures
             |
             v
APPROVED SERVER WORKFLOW
Upload/manage cookbooks, roles, environments, data bags
             |
             v
NODE CONFIGURATION
Assign recipe/role and environment as appropriate
             |
             v
CHEF CLIENT RUN ON NODE
Collect facts -> Resolve configuration -> Compile -> Converge
             |
             v
RESULT
Packages/files/services/accounts match declared requirements
             |
             v
OBSERVE
Review logs and verify results
```

This is a conceptual overview, not an exact ordering of every internal Chef substep or an approved company deployment procedure.

### Keep these distinctions clear

| Confusion | Correct understanding |
|---|---|
| "Cookbook folder equals node folder" | Cookbook source and node destinations are different locations |
| "files/ automatically appears on the node" | Resource destination controls the target path |
| "depends runs the other recipe" | It declares a dependency; inclusion/selection is separate |
| "Run-list is metadata.rb" | Node run-list and cookbook metadata are separate |
| "Compile creates the real file" | Normal compilation prepares resource definitions |
| "Converge always changes something" | Correct state can require no change |
| "Bash is automatically idempotent" | Script logic and guards determine repeat behaviour |
| "Marker exists, so application is healthy" | A marker guard only checks its stated condition |
| "Environment creates a VM" | It describes stage-specific Chef configuration |
| "Local JSON means server data is uploaded" | File creation and upload are separate |
| "chef-client -l debug only reads logs" | It starts a configuration run |
| "Verification fixes the file" | Verification assertions inspect results |
| "All examples belong in one recipe" | Examples are separate unless explicitly combined |

## 28. Coverage, remaining work, and a practice plan

### How the lessons progressed

- **Day 1:** Architecture, desired state, cookbook/recipe/resource basics, attributes, templates, static files, idempotency, guards, run-lists. Organization and Ohai were explained in conversation; the attached Day 1 text stops before those reminders.
- **Day 2:** Dependencies, recipe inclusion, libraries, resource examples, source/destination clarity, Bash idempotency, a small cookbook, roles, and environments.
- **Day 3:** Compile/converge, environment recap, attribute precedence basics, data bags, and encrypted-item introduction.
- **Follow-up:** Role/environment override comparison, encrypted-item reading, CLI introductions, logging/debugging, Kitchen configuration, and verification tests.

The source includes old pending lists from earlier lessons. A topic marked pending on Day 2 may have been introduced later. This is a timeline, not contradictory evidence of hands-on completion.

### Consolidated checklist

| Area | Theory coverage | Practical status |
|---|---|---|
| Architecture and components | Beginner explanation covered | Real setup not inspected |
| Organization and Ohai | Earlier-lesson reminders included | Not validated on a node |
| Cookbook structure and metadata | Covered with examples | Not generated/inspected in a lab |
| Dependencies and include_recipe | Covered | Upload/resolution not practiced |
| Recipes and listed resources | Basic examples covered | Not executed |
| Attributes and precedence | Basic comparisons covered | Full rules/deeper cases pending |
| Templates and static files | Covered | Output not validated |
| Idempotency and guards | Explained with examples | Repeated-run proof pending |
| Roles and environments | Purpose, locations, basic overrides | Upload/assignment pending |
| Data bags | Structure and reading examples | Upload pending |
| Encrypted items | Purpose and reading example | Encryption/provisioning pending |
| CLI tools | Purpose and sample commands | Setup/authentication/usage pending |
| Logging and debugging | Introduction covered | Real-log investigation pending |
| Test Kitchen | Configuration excerpt and workflow | Complete lab configuration pending |
| InSpec | Small verification example | Tests not run |
| Ansible and migration | Outside this Chef guide | Not started in these lessons |

### Remaining operational and deeper topics

- Approved lab selection and Chef Workstation setup.
- Chef Server connectivity, authentication, and permissions.
- Actual `knife` usage and cookbook upload.
- Node run-list, role, and environment assignment.
- Cookbook-version constraints and version pinning.
- Full attribute precedence details as needed.
- Data bag upload, encryption, and secure secret provisioning.
- Complete runnable Kitchen configuration.
- Converge/verify execution and failure analysis.
- Real logs and troubleshooting.
- Repeated-run/idempotency validation.
- Ansible learning and migration practice.

### Recommended small lab, once approved

1. Create a folder and a file using a small cookbook.
2. Apply it and inspect the result.
3. Apply it again and observe whether unnecessary changes occur.
4. Change the declared content and apply it again.
5. Verify the resulting content with a test.
6. Introduce a controlled failure, such as a missing parent directory.
7. Read the error, identify the cause, and fix it.

**Accurate status statement:** Chef foundational theory covered; practical validation pending.

No completion percentage in this guide is a measured skill assessment. Introductory topic coverage does not establish advanced knowledge or migration readiness.

## 29. Quick-reference glossary

| Term | Plain-English meaning |
|---|---|
| Chef | Configuration management using code |
| Desired state | The condition you want the node to satisfy |
| Workstation | Where code is authored and tested |
| Chef Server | Central configuration hub in the traditional model |
| Node | Managed machine |
| Chef Client | Applies configuration on the node |
| Organization | Server-side namespace/access boundary |
| Cookbook | Package of related automation and supporting files |
| Recipe | Configuration instructions |
| Resource | Declaration managing an item |
| Action | Operation requested from a resource |
| Attribute | Configuration value or node detail |
| Ohai | Collects machine facts as automatic attributes |
| Template | Text pattern rendered using values |
| cookbook_file | Manages a destination using a cookbook source file |
| Idempotency | Repeated application without unwanted extra changes |
| Guard | Condition controlling whether a resource action runs |
| Run-list | Node recipe/role selection and order |
| depends | Required cookbook declaration |
| include_recipe | Includes another recipe's resources |
| Library | Reusable Ruby helper code |
| Role | Common configuration for a server job/function |
| Environment | Deployment-stage configuration |
| Compile | Build/evaluate resource definitions in memory |
| Converge | Process resources and apply needed configuration |
| Precedence | Priority rules for competing attribute values |
| Data bag | Collection of JSON items |
| Encrypted item | Data protected with encryption and a secret |
| knife | Chef Server management CLI |
| Test Kitchen | Configured cookbook testing workflow |
| InSpec | Verification tool used in the test examples |
| Logging | Recording events/messages |
| Debugging | Investigating a failure and its cause |

## 30. Source and scope notes

**Primary source:** The user-provided `chef-notes.txt`, containing Day 1, Day 2, Day 3, and after-encrypted-data-bag notes.

**Editorial treatment:** Translated the mixed-language explanations into English, repaired escaped code characters, consolidated repetition, and added ASCII diagrams, file-location explanations, and expected-result descriptions. Supplemental material from the visible conversation is identified in the relevant sections.

**Internal reference preserved from the source:** [Chef Client Troubleshooting Guide](https://confluence.jnj.com/spaces/SCMX/pages/76994909/Chef+Client+Troubleshooting+Guide).

The internal troubleshooting page was not opened or reviewed for this guide. Its presence is a preserved reference, not verification of its contents.

**Example boundaries:** Names such as `web_setup`, `base_setup`, `appuser`, `appteam`, and example node names are learning examples. They do not describe verified company configuration. Linux examples and Windows examples are separate unless explicitly stated otherwise.

**Safety:** Do not run configuration commands against production or company-managed nodes without authorization. Never include real secrets in learning notes, recipes, source control, or logs.
