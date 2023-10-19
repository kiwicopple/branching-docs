# About branching

Branching lets you safely and easily experiment with changes to your Supabase project.

Supabase branches work similarly to Git branches. You can create a new branch for each set of related changes, such as new configurations, new database schemas, or new features. A temporary Supabase instance is created with your changes, so you can examine and test them without affecting your production instance.

When you’re ready to ship your changes, merge your branch. Your production instance updates to reflect the new changes.

<aside>
ℹ️ **Branching is an alpha feature**
We don’t recommend using it in a production environment.
To try out branching for non-production experiments, request access here.

</aside>

<aside>
ℹ️ **Branching is available on the Pro Plan and above.**

</aside>

## How branching works

Conceptually, Supabase branching works like Git branching.

It uses SQL migration files to manage database changes between branches. When you create a preview branch, the SQL migration files on that branch are run to create a preview instance. The preview instance reflects your current production schema, plus the migration changes.

When you merge a preview branch into the production branch, the associated migrations are run on the production instance.

Each branch has its own preview instance, which is deployed on its own isolated server.

<aside>
ℹ️ Only schema changes are captured in preview branches. Preview instances contain no data by default. You can include a seed file to seed your preview instance with sample data. 
Future versions of branching may allow for automated data seeding and cloning.

</aside>

## Branching workflow

When branching is turned on, each Supabase project can consist of multiple branches. Each branch is backed by an isolated Supabase instance. Each instance contains all Supabase features, and has its own API credentials.

![Each branch has a separate Supabase instance.](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/95bfe955-7da3-4745-ac27-f76c37098764/Screenshot_2023-09-27_at_2.46.48_AM.png)

Each branch has a separate Supabase instance.

Supabase uses Git operations to manage Supabase branching. Once you install the integration for your Git provider, Supabase watches all commits, branches, and pull requests. Each time a commit is pushed with new migrations in the `./supabase/migrations` directory, the migrations are run on the matching preview instance. The first time migrations are run on a preview branch, the new database is seeded with sample data based on `./supabase/seed.sql`, if it exists.

Only GitHub is currently supported (as a beta release). If you’re interested in other Git providers or a non-Git-based workflow, join the discussions for *GitLab Branching*, *BitBucket Branching*, and non-Git based Branching.

![Each GitHub branch can have its own Supabase preview branch.](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/7e916f0c-7f59-4fb6-8f66-bfe6f3c7fa65/Screenshot_2023-09-27_at_1.40.54_AM.png)

Each GitHub branch can have its own Supabase preview branch.

## Alternatives to branching

If you don’t turn on branching, your Supabase project continues to work as always. You have a single set of API keys for each project, and no preview instances are created.

If you prefer not to use branching, you can manage your environments and tests in other ways:

1. **Host a project per environment, and run tests against a staging project**
    
    Create multiple projects on Supabase with the same schema. Use one project as a staging environment to test any changes. Then migrate tested changes to the production project.
    
2. **Host a single production project, and run tests locally**
    
    Create a single project to host your production instance. Test any changes locally, then run the migrations against your hosted production project.
    

You can also combine both strategies to perform both local and staging tests.

# How to use Supabase branching

<aside>
ℹ️ Supabase branching requires the GitHub integration. Your application must be developed in a GitHub repository.

</aside>

## Prepare your GitHub repository

You need a GitHub repository to use Supabase branching. The `./supabase` directory in your repository contains your SQL migration files and your database seed file.

To start:

- If you don’t have a new project, [set one up using the example template](https://www.notion.so/Charis-Task-Branching-docs-a90d0b6697d54152a738af90a0573eb8?pvs=21).
- If you have a project, [make sure your database-related files are up to date](https://www.notion.so/Charis-Task-Branching-docs-a90d0b6697d54152a738af90a0573eb8?pvs=21).

### **Set up a new project with the example template**

Use the NextJS example template to try out branching. This template includes sample migration and seed files to get you started.

Run the following command in your terminal to clone the example:

```bash
npx create-next-app -e with-supabase
```

Push your new project to a GitHub repo. For more information, see the GitHub guides on creating and pushing code to a new repository.

### Set up an existing project for branching

Before enabling branching on an existing project, make sure your database-related files are up to date. You need a correct snapshot of your existing database, so newer migration files have a foundation to run on top of.

<aside>
⚠️ Setting up the initial state incorrectly can cause migrations to be wrongly applied. Follow the instructions below **************************************************************************before turning on Supabase branching**************************************************************************.

</aside>

**If your migration files are already in GitHub, under `./supabase/migrations`:**

If your migration files are up to date with your production database, you don’t need to do anything. You can now turn on branching.

**If you don’t have migration files in GitHub:**

You need to take a snapshot of your database. Log into Supabase from the command line client and pull the relevant files. Then commit them to GitHub:

1. Inside your project directory, initialize your configuration for Supabase local development:
    
    ```bash
    supabase init
    ```
    
2. Pull down database-related files. You can find your database URL
    
    ```bash
    supabase db pull --db-url <db_url>
    ```
    
3. Commit the `supabase` directory to Git, and push your changes to your remote repository.
    
    ```bash
    git add supabase
    git commit -m "Initial migration"
    git push
    ```
    

## Enable Supabase branching

Once your repository is [correctly prepared](https://www.notion.so/Charis-Task-Branching-docs-a90d0b6697d54152a738af90a0573eb8?pvs=21), you can enable branching from the Supabase dashboard.

<aside>
⚠️ Before turning on branching, make sure your [GitHub repository is prepared](https://www.notion.so/Charis-Task-Branching-docs-a90d0b6697d54152a738af90a0573eb8?pvs=21). If your repository doesn’t have all the migration files, your production branch could run an incomplete set of migrations.

</aside>

<aside>
ℹ️ **************************************Branching is available to alpha testers.**************************************
If you’re not in the alpha testing group, you can join the waitlist.

</aside>

1. Select the project you want to use, then click `Enable branching`.
If you’re prompted to join the waitlist, click `Join waitlist`. Access to branching will be expanded as branching graduates from alpha testing.
    
    ![Screenshot 2023-09-27 at 1.59.31 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/713c8ca0-f9f3-4c2d-ba3f-8dd158f9df47/Screenshot_2023-09-27_at_1.59.31_PM.png)
    
2. If you see the following dialogue, you’re in the alpha testing group. 
    
    ![Screenshot 2023-10-02 at 2.06.14 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/ba16c559-2d61-4ded-bd8c-e77827e4d449/Screenshot_2023-10-02_at_2.06.14_PM.png)
    
    If you don’t have the GitHub integration installed, click `Install GitHub Integration`. The integration is required to run migration files and the optional database seed file.
    
    You’re taken to the GitHub integration page. Click `Install`.
    
    ![Screenshot 2023-10-02 at 3.37.30 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/d5a7f613-1cbd-486a-b943-d2cb9ec2375d/Screenshot_2023-10-02_at_3.37.30_PM.png)
    
    Follow the instructions to link your Supabase project to its GitHub repository.
    
    ![Screenshot 2023-10-02 at 3.38.28 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/ced6a2d2-d59e-48ac-80d5-649c5d860769/Screenshot_2023-10-02_at_3.38.28_PM.png)
    
    Return to your project and re-click `Enable branching`.
    
3. Select the branch you want to use for production.
    
    <aside>
    ℹ️ **Your production branch can’t be changed while branching is enabled**
    To change your production branch, you need to disable branching and re-enable it with a different branch.
    
    </aside>
    
    ![Screenshot 2023-10-03 at 2.02.35 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/3d478f32-2cbe-42a2-b563-b645d02ce08f/Screenshot_2023-10-03_at_2.02.35_AM.png)
    
4. Click `I understand, enable branching`. Branching is now enabled for your project.

## Create your first preview branch

Preview branches are automatically created for each pull request, but you can also manually create one.

1. ********************************************************Create a new Git branch in your GitHub repository********************************************************.
You need at least one other branch aside from your Supabase production branch.
    
    ![You can use the GitHub dashboard or command line to create a new branch. In this example, the new branch is called `feat/add-members`.
    ](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/2d09cc72-2bc7-4335-ad7f-076890f086b5/Screenshot_2023-10-03_at_2.06.59_AM.png)
    
    You can use the GitHub dashboard or command line to create a new branch. In this example, the new branch is called `feat/add-members`.
    
2. ****************************************************************************************************************Navigate to the Branches page in your Supabase dashboard.****************************************************************************************************************
In the Supabase dashboard, look for the branch dropdown on the right-hand side of the top bar. It should be set to your production branch by default. Open the dropdown and click `Manage branches`.

![Screenshot 2023-10-03 at 2.04.18 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/24fc0f77-8a14-42b5-8491-ce0554bf5569/Screenshot_2023-10-03_at_2.04.18_AM.png)

1. **Create a Supabase preview branch.**
Click `Create preview branch`.
    
    ![Screenshot 2023-10-03 at 2.07.57 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/6d2b9548-0b2f-4e17-9c8f-4548315608af/Screenshot_2023-10-03_at_2.07.57_AM.png)
    
    Select the Git branch you want to use. Click `Create branch` to confirm.
    
    ![Screenshot 2023-10-03 at 2.08.11 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/b3aeeaa0-109f-4f76-92b4-cbffdcf4f276/Screenshot_2023-10-03_at_2.08.11_AM.png)
    

## Develop your app with Supabase branches

Supabase branching uses the state of your GitHub repository to apply migrations and seed preview instances. It checks the directory `supabase` at the top level of your repository.

The `supabase` directory should include:

- All SQL migration files, under the subdirectory `migrations`
- An optional `seed.sql` file, used to seed preview instances with sample data

The GitHub integration watches for changes in the `migrations` directory. When it detects a new migration file, it runs the new migration against the matching preview instance.

You can create new migrations either [locally](https://www.notion.so/Charis-Task-Branching-docs-a90d0b6697d54152a738af90a0573eb8?pvs=21) or [remotely](https://www.notion.so/Charis-Task-Branching-docs-a90d0b6697d54152a738af90a0573eb8?pvs=21).

Local development is recommended for developers familiar with the Supabase CLI, who want to test changes on their local machine. Remote development is recommended if you don’t want to run Supabase locally, or if you want to collaborate with team members via the dashboard interface.

### Develop locally

You can develop locally by running Supabase on your local machine. You’ll have a database running locally, and another database running remotely on the preview branch.

You can experiment on your local database by [writing SQL migrations yourself](https://supabase.com/docs/guides/cli/local-development#database-migrations). You can also [auto-generate migration files by using Supabase’s local studio](https://supabase.com/docs/guides/cli/local-development#diffing-changes) and the `supabase db-diff` command.

Once you’re confident in your migration files, push your changes to your remote GitHub repository to update the preview branch.

To create and push local migrations:

1. Create a new migration file.
    
    ```json
    supabase migration new "new_feature"
    ```
    
2. Add SQL statements to your new migration file. You can manually edit the new migration file, or use [local studio](https://supabase.com/docs/guides/cli/local-development#diffing-changes) and diff the changes.
    
    ```sql
    create table employees (
      id integer primary key generated always as identity,
    	name text
    )
    ```
    
3. Commit and push your migration file.
    
    ```sql
    git add supabase/migrations
    git commit -m "Add employees table"
    git push --set-upstream origin new-employee
    ```
    

Supabase’s GitHub integration detects the new migration and runs it against the branch instance.

It can take up to 10 minutes for migrations to be applied. If you have a PR for your branch, errors are reflected in the GitHub check run status and in a PR comment.

If you need to reset your database to a clean state (that is, discard any changes that aren’t reflected in the migration files), run `supabase db reset` locally. Then, delete the preview branch and recreate it.

### Develop remotely

As an alternative to developing locally, you can make changes on your remote Supabase dashboard. You can then pull these changes to your local machine and commit them to GitHub.

<aside>
ℹ️ Changes must be locally pulled and committed to keep your GitHub repository state in sync. Dashboard changes aren’t automatically reflected in your repository. If you’d like to see automatic syncing in a future release, join the discussion.

</aside>

![Screenshot 2023-10-03 at 1.15.26 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/30c810a1-b97f-4030-aec8-b48389a81d28/Screenshot_2023-10-03_at_1.15.26_PM.png)

1. Click on the active branch in the top bar to open the dropdown menu. Select the branch you want to use.
2. Make changes to your database schema from the Supabase dashboard.
3. In your terminal, navigate to the directory for your Supabase project. Use the Supabase client to pull changes from your branch to your local migrations directory. Make sure to use the database URL for your branch.
    
    ```bash
    	supabase db pull --db-url "postgres://postgres:[password]@db.[branch-ref].supabase.co:5432/postgres"
    ```
    
4. Commit and push your migration file. No new migrations are run. This is expected, because your database is already up-to-date, based on the changes you made in the dashboard. But this ensures that your migration files are in GitHub, so they can be correctly merged into production.

# WIP

<aside>
🛑 Stuff below this I didn’t get to yet

</aside>

## Open a pull request to your production branch

At some point during development you will need to open a pull request with the intention of merging your changes into your production branch. The Supabase integration handles this process for you by commenting on your Pull Request details of migration files.

When you open a pull request, Supabase will check if a Preview Branch is already running for that git branch, if not, it will create a new one for you automatically. If you have already created a Preview Branch for that git branch, the same Preview branch will be used for your pull request.

![Screenshot 2023-10-05 at 1.55.27 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/bb45f667-7773-41aa-a0d7-4c48b369b0a9/Screenshot_2023-10-05_at_1.55.27_AM.png)

The Pull Request comment will show the deployment status of the Preview Branch, which are split into Database, Services and APIs. Once they are running, the table below called Tasks shows Migrations status, along with the Seeding status.

On every git push, the GitHub integration will check if there are any changes in the `./supabase/migrations` directory, and if so, will run those migrations against your Preview Branch database.

## Disable branching

You can disable branching at any time:

1. Navigate to the Branches page.
2. Click the menu button in the Production Branch panel.
3. Click `Disable branching`.

![Screenshot 2023-10-05 at 2.42.16 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/165a3c21-8e38-4d4b-831a-f9bb88a3262b/ad6b6fb5-f153-4152-be74-abde233372a0/Screenshot_2023-10-05_at_2.42.16_PM.png)

### How migrations are applied

Migrations are executed in a sequential order, and each migration builds on top of the previous ones. The database in the Preview Branch keeps track of which migrations have been applied, and this forms a stack or chain of applied migrations.

However, this could present an issue with [rolling back](https://www.notion.so/Charis-Task-Branching-docs-a90d0b6697d54152a738af90a0573eb8?pvs=21) changes.

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

- add issues with paid accounts can only use branching for now @Jonny Summers
- make sure its clear prod branch can’t be changed @Jonny Summers
- make sure we cover the seeding issue - its technically schema branching right now, not data branching