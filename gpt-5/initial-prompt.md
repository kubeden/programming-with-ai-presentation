Hi we are going to be doing some work on a new project I'm going to build.

It is an important project, so it is important that we get the things correctly from the beginning.
It is going to be a NextJS project, so here is some of the information on how I want the structure to be:

What the app is going to be about?
The application we are going to be building is a cloud-based markdown editor & notes app.
Here are the features we are going to need:
 - Functionality
 - - We should have authentication
 - - We should be able to click Cmd + s to save the text
 - - We should be able to have document versions. Each save generates a new version.

 - UI
 - - General View: sidebar on the left + editor on the right
 - - - the editor on the right should either be a single-pane editing, or a two-pane editor + viewer. the states should be toggleable
 - - Version Sidebar: a right sidebar that contains the current version, and the past versions (with previews, like in Google docs). Clicking a version should trigger a flow inside the card that asks the user if they want to go to that version, then when the user confirms, we should render this text.
 - - There should be a bottom status bar that on the left has status about the page (character count, lines, current cursor), and on the very right a circle + text if it is "saved" or "editing". When the text is not what it is in the database, we should have the circle yellow, otherwise it should be green.
 - - There should also be a top bar where there is an input box on the left for the title, and the versioning controls on the right (with font awesome)

We are going to be hosting the application on our own k8s cluster.
We will be using Supabase for our database.
Although.. we will be writing our own API actions in our NextJS api, so we are using Supabase strictly for:
- database
- authentication

The migrations and database set-up is going to happen inside:
- /supabase
- - /supabase/migrations
- - - /supabase/migrations/{migration}.sql

The database schema structure should be:
- users
- - user id (with a trigger from the auth table)
- - user name (automatically generated through their email + unique identifier to make sure it is always available)
- - user email (trigger from the auth table)
- documents (with a relation to users + on delete user cascade)
- - id
- - user id
- - document title
- - document body
- - current version id
- - timestamps
- document_versions (with a relation to documents + on delete document cascade)
- - id
- - document id
- - body
- - created at

We should write correct RLS polciies, where each user will only be able to edit their own records.
It is OK that we use supabase-js and do these from the client.

Authentication should only happen with OTP and the flow should look like this:
- user enters website, they see the editor
- in the sidebar, on the top-left there is a login button
- the user can click the button to log in, which will trigger a full page width & height authentication modal
- the user types their email, and clicks a button "log in"
- - we make a check to see if this email is part of our authentication, and if it is not, he enters a sign-up flow
- - - "you are not registered. We have sent a one-time password that will let you log into your account"
- - - they receive the OTP on their email
- - - they enter the OTP
- - - they click sign up
- - - they are authenticated
- - if the user email exists, they receive the otp, and they sign in

^ we are going to be using Supabase's OTP

---

We are going to be using the following libraries (and more):
- app router
- tailwind
- codemirror
- remark
- @supabase/ssr
- @supabase/supabase-js
- font awesome

---

Our directory structure should look like this:
- /src/middleware.js
- /src/app
- - /src/app/page.js
- - /src/app/layout.js
- - /src/app/{page}/page.js
- - /src/app/{page}/actions.js
- /src/app/api
- - /src/app/api/{route}/route.js + {page}-level action.js
- /src/app/components/
- - /src/app/components/{component}
- - - /src/app/components/{component}/{component-modules}/{component-module.js}
- - - /src/app/components/{component}/main.js

^ where:
- {page} is the /page
- {component} is the respective UI component
- {component-module} is each file, responsible for a group of functionalities that component is going to be responsible for

We are going to be focusing on modularity and code readability.

---

The color scheme that we are going to be using for this project is:
- white, black, and different shades of gray

The style should be minimalistic and clean, but also elegant.

Clarifications:
- Client vs server: I prefer calling DB via via Next.js route handlers/server actions
- No auto-save.. only ctrl/cmd+s to save
- Title should be default "Untitled" and the input bar should be in a top-bar where the user can edit it. No automatic titling from the first H1.
- 