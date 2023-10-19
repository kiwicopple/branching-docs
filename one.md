Branching is a Supabase feature similar to Git branching, that allows you to create multiple Supabase projects to easily test out new features, experiment with different configurations, and collaborate with others on changes to your project - without affecting or risking your main production environment.

<aside>
ℹ️ **Branching is a subscription feature**
Your organization needs to be on the Pro Plan or above.

</aside>

# How does branching work?

Supabase Branching follows common industry patterns, using the concept of SQL migrations to manage the database state between different instances of your project. We deploy each Preview Branch in it’s own isolated server.

Developers add a series of SQL migration files that are run on the Preview Branch, and when you merge a Preview Branch into the Production Branch, those migration files will be on your Production database.

## Schema branching vs Data branching

Supabase Branching is currently a schema branching solution, and doesn’t cover data branching. We have a rough roadmap to include automated data solutions, like data seeding and cloning.

## Using Supabase with Branching

With Branching in use, your Supabase project will have a collection of APIs/keys for multiple branches. Each branch runs in an isolated instance that includes all Supabase features.

With Supabase Branching….

![Screenshot 2023-09-27 at 2.46.48 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/95bfe955-7da3-4745-ac27-f76c37098764/Screenshot_2023-09-27_at_2.46.48_AM.png)

There are various *workflows* that developers can use*, to* accommodate the different needs of individuals and teams.

There are two distinct workflows that Supabase Branching provides:

1. **[Git based** **workflows](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21)**
Using integrations installed to your repository in your preferred git provider, Supabase will watch all commits, branches, and pull requests. When a new commit is pushed, and if new migrations are detected in the `./supabase/migrations` directory, the Preview Branch will run those new migrations.
    
    We are working towards providing integrations for the following popular Git services:
    
    - GitHub - Currently a BETA release - **Install Link**
    - GitLab - Not in active development. 
    *Sound interesting? Join the discussion for GitLab Branching.*
    - BitBucket - Not in active development.
    *Sound interesting? Join the discussion for BitBucket Branching.*
2. **[Non git based workflow](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21)**
A Branching workflow without the need for using a Git based provider. We do not support this workflow yet. 
*Sound interesting? Join the discussion for* non-git based Branching.

## Git based workflow

The following is based on using GitHub, which is currently the only Git provider we have an integration for, although [GitLab](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21) and [Bitbucket](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21) are tentatively on our roadmap.

The most obvious relation between Git and Supabase Branching is that both support the concept of a ‘branch’. You’ll be able to choose which GitHub branch best represents “production” for your use case (usually this is the ‘default branch’ in GitHub, but it can be any branch). For every other Git branch you have, you can make a corresponding Preview Branch for that Git branch.

![Each GitHub branch can have it’s own corresponding Supabase Preview Branch.](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/7e916f0c-7f59-4fb6-8f66-bfe6f3c7fa65/Screenshot_2023-09-27_at_1.40.54_AM.png)

Each GitHub branch can have it’s own corresponding Supabase Preview Branch.

For git based workflows your GitHub repository will need a supabase directory. The default for this is `./supabase` in the root of your repository.

Within this directory, 

- we keep all the SQL migrations of the Project (`./supabase/migrations/**`).
- we keep a seed file for seeding Preview Branches with sample data (`./supabase/seed.sql`)

The Supabase GitHub integration will watch for file changes within this directory and if any  migration file file is added, the GitHub integration will run the migrations on the Branch Preview.

Let’s take an example; you have been developing locally, with a Supabase database locally. At some point, you may have decided to proceed with the schema changes you’ve made in your local database. You may run a CLI command such as `supabase db diff -f new-members`.

A new SQL file will have been created called `[timestamp]_new-members.sql`, and you will have checked in, committed, and pushed this file to your git branch. Once pushed, the GitHub integration will see that new migration file, and then run that new migration on the Preview Branch.

![GitHub integration will run any new migrations detected in your git branch on the Preview Branch.](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/45dd1c22-b368-43c9-8cfc-d67ea54bda7b/Screenshot_2023-09-27_at_1.52.24_AM.png)

GitHub integration will run any new migrations detected in your git branch on the Preview Branch.

You may need to update your schema remotely, via the Studio Dashboard. However you’ll need to [manually pull those changes to your local machine](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21) so that you can commit those changes to GitHub.

<aside>
ℹ️ **Remote dashboard currently does not commit changes to GitHub**
As of writing, you will only be able to work on migration and seed changes manually, via GitHub, making the migration changes yourself.

We are actively investigating the Remote Dashboard being able to commit schema changes to GitHub; join the conversation on GitHub discussions.

</aside>

We are investigating allowing Preview Branches to ‘commit’ schema changes from the dashboard directly to your GitHub repository on your behalf.

![github-flow-default-commit-migration.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/8e08661d-26d5-4fe4-b306-a057b9456c3f/github-flow-default-commit-migration.png)

## Non git based workflow

<aside>
ℹ️ This is not in active development yet

</aside>

We have a rudimentary plan in place to support the concept of a non git based workflow for Branching. As of writing, we are currently dedicated to providing 1st class support for git based workflows before we move on to a new type of workflow.

In the meantime, you can show interest in non-git based Branching by joining our GitHub discussion.

## Using Supabase without branching

With Branching not in use, your Supabase project will only have 1 set of APIs/keys for 1 instance.

It’s important to understand that Branching might not be the best solution for everyone, so let’s explore some of the alternatives that Supabase developers opt for without Branching.

1. **Multiple hosted projects, testing remotely**

Developers will spin up multiple hosted projects on supabase, all with the same schema.
they would then develop against the “staging/testing” project with the changes they want to make, and then migrate those changes to the production project.

1. **One hosted project, testing locally**

Developers will have 1 hosted project, and they will work against a local project while developing their project, on their local machine. Once they are happy with the changes they will run the migrations against their hosted production supabase project.

1. **A bit of both**

Developers will also work locally against a local db, and then run those migrations against a ‘staging/testing’ remote Supabase project.

# Getting started

<aside>
ℹ️ Supabase branching currently requires GitHub, and for your application to be developed in a GitHub repository, along with SQL migration files.

</aside>

Follow these steps to get started with Branching

## Preparing your GitHub repository

You will need a GitHub repository to work with Supabase Branching. Your repository will be the source of truth for migrations and seed.sql which will run on your Preview Branches, and once merged into your Git production branch, will be used to run against your database.

### **Starting a new template**

We recommend using the create-next-app supabase NextJS example template if you are just starting out and wanting to experiment with Branching. This will give you all the sample migration and seed files you need to get going.

```bash
npx create-next-app -e with-supabase
```

You will need to push this git repo to a new GitHub repo. You can follow the GitHub guides on creating and pushing code to a new repository.

### Pre-existing projects

For those that already have projects, you will need to ensure that you have the correct files in GitHub before using Branching. It is important to follow these guides if so.

**I have migration files in GitHub**

As long as the migration files in your repository are up to date with what you have in production, then you will not need to do anything, you can proceed to opt into branching.

**I don’t have migration files in GitHub yet**

You’ll need an initial snapshot of your database if you haven’t used migrations before. This will be the initial foundation in which newer migration files will be run on top of so that your database can be recreated for Preview Branches.

By default, your migration file will be saved in the `./supabase/migrations` directory, unless you have changed the default directory.

```bash
-- init link and pull 
supabase init
supabase login
supabase link --project-ref <ref>
supabase db pull

-- OR
supabase init
supabase db pull --db-url <db_url>

-- 
git add supabase
git commit -m "Initial migration"
git push
```

## Opting into Branching

If you’ve made sure 

1. In any of your projects, click the ‘Enable branching’ button
If you are not in the alpha testing group, then you can join the waitlist.
    
    ![Screenshot 2023-09-27 at 1.59.31 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/713c8ca0-f9f3-4c2d-ba3f-8dd158f9df47/Screenshot_2023-09-27_at_1.59.31_PM.png)
    
    For those that are in the alpha test group, you will see a dialog like this:
    
    ![Screenshot 2023-10-02 at 2.06.14 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/ba16c559-2d61-4ded-bd8c-e77827e4d449/Screenshot_2023-10-02_at_2.06.14_PM.png)
    
2. **Install the GitHub integration**
Branching requires a GitHub connection to the GitHub repository you are using so that it can run migration files and run the optional seed.sql file.
    
    ![Screenshot 2023-10-02 at 2.08.44 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/cc9e1939-a6fb-427f-827f-89b27ed8b11a/Screenshot_2023-10-02_at_2.08.44_PM.png)
    
    ![Screenshot 2023-10-02 at 3.37.30 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/d5a7f613-1cbd-486a-b943-d2cb9ec2375d/Screenshot_2023-10-02_at_3.37.30_PM.png)
    
    ![Screenshot 2023-10-02 at 3.38.06 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/e03f176c-9cad-4395-8bdf-2aa722fd9c33/Screenshot_2023-10-02_at_3.38.06_PM.png)
    
    ![Screenshot 2023-10-02 at 3.38.28 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/ced6a2d2-d59e-48ac-80d5-649c5d860769/Screenshot_2023-10-02_at_3.38.28_PM.png)
    
    ![Screenshot 2023-10-02 at 3.38.56 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/027dd300-5ab2-4158-a3ba-69347ca15087/Screenshot_2023-10-02_at_3.38.56_PM.png)
    
    If this is the first time you’ve installed the Integration, you will need to now return back to the project and re-click “Opt into branching”
    
3. You’ll now have the GitHub integration installed.
Pick the branch you want to use for production and continue.
    
    <aside>
    ℹ️ **Production branch cannot be changed once opted in**
    Currently the Production Branch cannot be changed, and you will need to opt out of branching and opt back in to use a different branch
    
    </aside>
    
    ![Screenshot 2023-10-03 at 2.02.35 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/3d478f32-2cbe-42a2-b563-b645d02ce08f/Screenshot_2023-10-03_at_2.02.35_AM.png)
    

### Creating your first Preview Branch

- **Create a new Git branch in your GitHub repository**
In your GitHub repository, you’ll need to make sure you have at least 1 extra branch other than the git branch you chose for your Production Branch in Supabase.
    
    ![In this example, we have made a new branch in our GitHub repository called ‘feat/add-members’
    ](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/2d09cc72-2bc7-4335-ad7f-076890f086b5/Screenshot_2023-10-03_at_2.06.59_AM.png)
    
    In this example, we have made a new branch in our GitHub repository called ‘feat/add-members’
    
- **Go to the new Branches page**
In the Supabase dashboard, you should now be able to see a new navigation dropdown on the right. This is our branch dropdown for you project. Using this dropdown menu you’ll be able to switch Preview Branch. Right now, we only have 1 branch, click on ‘Manage Branches’.
    
    ![Screenshot 2023-10-03 at 2.04.18 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/24fc0f77-8a14-42b5-8491-ce0554bf5569/Screenshot_2023-10-03_at_2.04.18_AM.png)
    

- **Create a Supabase Preview Branch in the Branches page.**
In the Branches page, just click ‘Create preview branch’.

In the Branches page, just click ‘Create preview branch’.
    
    ![Screenshot 2023-10-03 at 2.07.57 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/6d2b9548-0b2f-4e17-9c8f-4548315608af/Screenshot_2023-10-03_at_2.07.57_AM.png)
    
    You’ll see a list of Git branches from your repository here, click ‘Create Branch’ for the GitHub git branch you wish to base your Supabase Preview Branch on.
    
    ![Screenshot 2023-10-03 at 2.08.11 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/b3aeeaa0-109f-4f76-92b4-cbffdcf4f276/Screenshot_2023-10-03_at_2.08.11_AM.png)
    

### Disabling Branching

If you wish to stop using Branching, you can disable the feature at any time. Go to the Branches page and click the ellipsis button in the Production Branch panel and click “Disable Branching” in the dropdown menu.

![Screenshot 2023-10-05 at 2.42.16 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/ad6b6fb5-f153-4152-be74-abde233372a0/Screenshot_2023-10-05_at_2.42.16_PM.png)

# Developing with Branching

<aside>
⚠️ **It is important to [prep your git repository first](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21)**
It’s possible your Production Branch could run migration files from your `./supabase/migrations` without having all the historical migrations.

</aside>

Branching currently works with only Git and GitHub today, so you will need to choose a development strategy for capturing migration changes and committing them into your GitHub repository. This could either be done with [remote based development](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21) or [locally based development](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21).

## Develop locally

You can develop locally by running Supabase on your local machine, which will allow you to either write [SQL migrations yourself](https://supabase.com/docs/guides/cli/local-development#database-migrations), or [SQL migrations created with db diff (local studio)](https://supabase.com/docs/guides/cli/local-development#diffing-changes) `supabase db-diff` command.

This means you can have a database running locally, and you’ll also have a database running remotely on the Preview Branch. This gives you some freedom to experiment rapidly locally and then only commit migration files to the git branch when you are more confident. 

Remember, it is easy to `supabase db reset` locally however if you want to run all the migration files again in the Preview Branch (essentially the same as running `supabase db reset`) then you will need to delete the Preview Branch, and create it again.

The most simplest method to get started with local development for Branching is to create migrations yourself:

1. Create a new migration file
    
    ```json
    supabase migration new "new_feature"
    ```
    
2. Add SQL statements to your new migration file. This can be done either by manually editing the new migration file or diffing changes through [local studio](https://supabase.com/docs/guides/cli/local-development#diffing-changes).
    
    ```sql
    create table employees (
      id integer primary key generated always as identity,
    	name text
    )
    ```
    
3. Commit and push your migration file
    
    ```sql
    git add supabase/migrations
    git commit -m "Add employees table"
    git push --set-upstream origin new-employee
    ```
    

The new migration will be picked up by Supabase GitHub App and run on the branch project. Any migration errors will be surfaced as PR comment and GitHub check run status.

Please note that when pushing new migration changes to GitHub, it could take up to 10mins for the migration changes to apply to your Preview Branch.

**Why choose to develop locally?**

Developers familiar with using the Supabase CLI will feel comfortable using this local development setup.

You also have the choice of using the local development Studio to make schema changes and creating migration files with CLI, or writing your own migration files manually.

## Develop remotely

You can work with your remote Project, and make schema changes on the remote Dashboard. You can then capture those changes periodically, and pull them down to your local machine, at which point you can then commit them into your GitHub repository.

![Screenshot 2023-10-03 at 1.15.26 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/30c810a1-b97f-4030-aec8-b48389a81d28/Screenshot_2023-10-03_at_1.15.26_PM.png)

Pull changes from your branch project to your local migrations directory by specifying the `db_url` of your branch project.

```json
supabase db pull --db-url "postgres://postgres:[password]@db.[branch-ref].supabase.co:5432/postgres"
```

Commit and push your migration file. The Supabase GitHub App should pick the new migration file. However, it should be noted that the migration file you pushed will not run on the preview project because there are no changes to be made. 

That might sound counter productive, but the need for pushing the migration files to GitHub are so you can merge schema changes from Pull Request into your production branch.

Please note that when pushing new migration changes to GitHub, it could take up to 10mins for the migration changes to apply to your Preview Branch.

**Why choose to develop remotely?**

This will be suitable for developers that don’t wish to run Supabase locally, which requires running several Docker containers that might be a drain on your local machine resources.

It’s also worth considering that multiple team members can use the remote dashboard at the same time, so collaboration could be easier through the remote dashboard, and then one team member can run `supabase db pull` to make sure the schema changes are captured into the Git repository.

## Pull requests into Production branch

At some point during development you will need to open a pull request with the intention of merging your changes into your production branch. The Supabase integration handles this process for you by commenting on your Pull Request details of migration files.

When you open a pull request, Supabase will check if a Preview Branch is already running for that git branch, if not, it will create a new one for you automatically. If you have already created a Preview Branch for that git branch, the same Preview branch will be used for your pull request.

![Screenshot 2023-10-05 at 1.55.27 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/bb45f667-7773-41aa-a0d7-4c48b369b0a9/Screenshot_2023-10-05_at_1.55.27_AM.png)

The Pull Request comment will show the deployment status of the Preview Branch, which are split into Database, Services and APIs. Once they are running, the table below called Tasks shows Migrations status, along with the Seeding status.

On every git push, the GitHub integration will check if there are any changes in the `./supabase/migrations` directory, and if so, will run those migrations against your Preview Branch database. 

### How migrations are applied

Migrations are executed in a sequential order, and each migration builds on top of the previous ones. The database in the Preview Branch keeps track of which migrations have been applied, and this forms a stack or chain of applied migrations.

However, this could present an issue with [rolling back](https://www.notion.so/Branching-docs-4a8e905463944a2897cd5511adb2569d?pvs=21) changes.

### **Rolling back migrations**

In some circumstances, you may want to roll back changes you’ve made in an earlier migration change. For instance, you’ve pushed a migration file that contains schema changes that you’ve decided you no longer want, or perhaps want to change. 

To remedy this, you will need to push your latest migration changes first, and then delete the Preview Branch and reopen it. The Preview Branch will be seeded again using your seed.sql file, so any additional data inserts you made from the old deleted Preview Branch will be lost. This is effectively the same as running `db reset` in the CLI locally, so the database is completely reset, and all the migrations are all run again in sequential order.

### Seeding behaviour

Seeding will only happen on Preview Branch creation, if you need to re-run the seeding, you will need to delete the preview branch in the dashboard (or via the CLI) and create a new Preview Branch again.

### When things go wrong

The GitHub integration will check if migrations need to be run on every push commit, and if there are migration changes in `./supabase/migrations` it will always run those new migrations. This will sometimes fail though, for example, the SQL in the migration file is invalid, or perhaps there is a conflict with part of the schema change you are doing.

The error (and warning) messages for these will be displayed in your PR in the integration’s comment near the top of the Pull Request comment thread. The check being run will also respond as a failed check.

**Github “Check required” setting**

We **highly recommend** that you turn on a ‘required check’ for the Supabase integration in your repository setting. This will restrict your Pull Requests from being merged when the migration checks fail on a Pull Request. This is especially important to restrict migration changes being merged into your production branch.

![Screenshot 2023-10-05 at 2.17.15 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/310f07eb-153d-4241-9fdf-ba5c4b10c7b4/Screenshot_2023-10-05_at_2.17.15_AM.png)

To add a check required setting, you will need to add a [branch protection rule](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches), and make sure to check “**[Require status checks to pass before merging](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches#require-status-checks-before-merging)", and finally also add “supabase” as one of the checks.**

## Migration Drifting

At some point, you may have multiple Preview Branches open with different schema changes being applied in each. During this development, one of those Preview Branches will be merged into your Production Branch. This will mean there is a schema drift between your Production Branch and your other Preview Branches you haven’t merged yet.

To resolve this conflict, you can either merge or rebase from your production git branch to your preview git branch. Since Branching is using git for migration files, you will need to ensure that all the migration files are timestamped correctly in chronological order after rebase. This means the new migration added in your preview git branch should always have the latest timestamp.

## Changing Production Branch

It’s currently not possible the change the git branch used for Branching Production Branch. The only way to change it is to “opt out” of branching and then “opt in” again.

You can “Disable Branching” by going to the Branches page and clicking the ellipsis button in the Production Branch panel.

![Screenshot 2023-10-05 at 2.42.16 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/ad6b6fb5-f153-4152-be74-abde233372a0/Screenshot_2023-10-05_at_2.42.16_PM.png)

# Branching and hosting providers

Branching works well with hosting providers that deploy preview deployments. These deployments can use Supabase Preview Branches that are based on the same git branch that the hosting provider is deploying from, thus allowing you to rapidly QA/test your application in a deployed environment while using up to date migrations from your Supabase Preview Branch.

We have tentateively planned to support Vercel and Netlify. You can also let us know what other hosting providers or concepts might be useful in the GitHub discussions forum.

## Vercel Integration

<aside>
🏗️ **Vercel Branching support currently in development**
As of writing, we are still working towards support for our Vercel Integration to work with Supabase branching. If your interested, you can join the discussion on GitHub discussions

</aside>

The Vercel integration can be installed either via the Vercel marketplace or by using any of the blue ‘Deploy’ buttons you may see in our app example readme files.

You will need to install the integration and link the same repository in GitHub your using for Branching to the same Supabase project.

<aside>
ℹ️ **You will need to use the Vercel GitHub Integration**
For Branching to work with Vercel, you will need to use the Vercel GitHub integration as well, so that Vercel is deploying previews based on your git repository.

</aside>

Supabase will update your Vercel project with environment variables for the git branch that the Preview Branch is based on. If any Vercel preview deployments are deployed from the same git branch, then that deployment will use the environment variables set by Supabase for the Preview Branch.

## Netlify

<aside>
🛑 This is not in active development yet

</aside>

We have a rudimentary plan in place to support Branching with a Netlify integration.As of writing, we currently do not have an active spec in place for delivering this integration. It’s possible we may also abandon plans for this integration.

In the meantime, you can show interest in Netlify with Branching by joining our GitHub discussion.

# Feedback

Supabase branching is a new and exciting new part of the Supabase development ecosystem, but we’ll be continually monitoring it’s success and take any feedback seriously and perhaps also take drastic action to change the conceptual direction we have made with Branching.

You can join the conversation over in GitHub discussions.

- Also cover adding a ‘required’ check for the Supabase bot check @Jonny Summers
- cover issues with paid accounts only @Jonny Summers
- cover issues re. ./supabase directory can’t be changed @Jonny Summers
- cover issues re. prod branch can’t be changed @Jonny Summers
- cover the seed issue (its technically schema branching right now, not data branching)