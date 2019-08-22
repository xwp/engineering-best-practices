# Agile Scrum

### Agile Ceremonies

For most Teams as a Service projects we recommend following an Agile Scrum methodology to develop software. The following Agile ceremonies and other events should be planned as part of a two week sprint cycle that XWP intends to follow while implementing user stories as defined within JIRA. These ideally have participation from XWP and the client, whether in a meeting synchronous or asynchronously \(e.g., via Zoom, JIRA, or Slack\).

#### **Backlog Grooming**

**Purpose**: JIRA tickets in the backlog are described by the Product Owner and Project Manager \(XWP\), validated by the client, and then assigned [story points](agile-scrum.md#story-point-reference) \(not specific values as days/hours/etc.\) by XWP using a service like [planning poker](https://www.planningpoker.com/).

**Note**: A representative from the client can asynchronously review the description and acceptance criteria from the top of the backlog.

**Attendees**: Product Owner \(XWP\), Project Manager \(XWP\), Lead Engineer \(XWP\), all Engineers \(XWP\) available, and Product Owner \(client\).

**Frequency**: Backlog Grooming will occur throughout the project and is time boxed by the amount of time attendees can devote to it each time.

#### **Sprint Planning**

**Purpose**: The XWP team will commit to the completion of a subset of the highest ranked backlog items. This commitment defines the sprint backlog and is based on the XWP team’s velocity or capacity.

The Project Manager \(XWP\) will facilitate the meeting; the Product Owner \(XWP\) and Product Owner \(client\) will represent the detail of the backlog items and their acceptance criteria; the XWP team defines the tasks, volunteers to own the tasks, and estimates the amount of effort to fulfill the commitment; and Product Owner \(client\) approves the stories included in the sprint.

Each XWP team member should fill ~80% of their capacity during planning and leave a ~20% buffer to handle QA issues or to work on late breaking, urgent and important tickets.

**Attendees**: All XWP team members, and any stakeholders needed from the client team.

**Frequency**: Sprint Planning will occur on the second Wednesday of each two week sprint, will be focused on the upcoming sprint, and will be time boxed to 2 hours.

#### **Daily Standup or Stand Down**

**Purpose**: XWP will present daily commitments and issues asynchronously using Posts in a designated Slack channel.

The XWP Team will cover the following questions:

1. What did you do yesterday?
2. What will you do today?
3. Are there any impediments in your way?

All XWP team members will review others daily posts in a shared Slack channel and provide comment as relevant. The Project Manager will expedite the resolution of any impediments.

**Attendees**: Product Owner \(XWP\), Project Manager \(XWP\), and all Engineers on the XWP team.

**Frequency**: Each XWP team member adds their daily standup or stand down post to the shared Slack channel on the same rhythm every day.

#### **Sprint Review / User Acceptance Testing**

**Purpose**: The Sprint Review will be facilitated by the Project Manager \(XWP\) with any interested stakeholders from the client and will include videos from the XWP team demonstrating that the functionality from each user story has been completed.

In lieu of attending the meeting, XWP team members will record a video demo for their work and any questions about the work can be addressed by the XWP team in attendance. Product Owner \(client\) will confirm that each user story is complete and mark it as accepted.

**Attendees**: Project Manager \(XWP\), Product Owner \(XWP\), Product Owner \(client\), and any representatives from the client team who can and want to attend.

**Frequency**: The Sprint Review will occur on the Tuesday after the completion of a sprint and will be time boxed to 1 hour.

#### **Retrospective**

**Purpose**: The Retrospective will be facilitated by the Project Manager \(XWP\) and will include the XWP team covering the following questions:

* What did we do well?
* What should we have done better?

Based on the answers to those questions, the XWP team will decide on actions to improve in the future. Google Docs will be used to track all items identified in the Retrospective.

**Note**: The Project Manager \(XWP\) will create a document listing the questions and each team member will add their responses and comment on input from the rest of the team that is sent out the Monday after the sprint. After all team members add their answers, they will meet synchronously to discuss and agree on action items on how things can iteratively improve.

**Attendees**: All XWP team members.

**Frequency**: Occurs the Wednesday after each sprint and will be time boxed to 1 hour.

#### **Bug Scrub**

**Purpose**: This isn’t a physical meeting, but a recurring task for certain team members to review the list of bugs to see if any have been resolved since they were identified and as such can be marked as Resolved in JIRA.

**Note**: All bugs identified during QA testing or Regression testing will be added as a “defect” in JIRA, tagged to the respective user story, sized during Backlog Grooming, and estimated during Sprint Planning.

**Attendees**: QA \(XWP\), Project Manager \(XWP\), and any others from the XWP team who can attend.

**Frequency**: Bug Scrubs typically occur once a week initially and can change in frequency.

#### **Code Releases**

**Purpose**: All code that has passed Regression Testing is eligible to be deployed to Production and should be deployed by the client, hosting partner, or through automation. XWP will tag a release in GitHub and denote which JIRA user stories, defects, and any other plugin updates are included.

**Frequency**: Code Deploy occurs on the second Monday after the last day of the sprint so that it’s before the Sprint Review and is not on a Friday.

### **Story Point Reference**

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>XS</b>
      </th>
      <th style="text-align:left"><b>S</b>
      </th>
      <th style="text-align:left"><b>M</b>
      </th>
      <th style="text-align:left"><b>L</b>
      </th>
      <th style="text-align:left"><b>XL</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>1-2</b>
      </td>
      <td style="text-align:left"><b>3</b>
      </td>
      <td style="text-align:left"><b>5</b>
      </td>
      <td style="text-align:left"><b>8</b>
      </td>
      <td style="text-align:left"><b>13</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>- Text Changes</p>
        <p>- Label Changes</p>
        <p>- Add a field</p>
        <p>- One line of code</p>
      </td>
      <td style="text-align:left">
        <p>- 1 or 2 files changed</p>
        <p>- Self contained - Targeted change</p>
        <p>- Requires UI testing</p>
      </td>
      <td style="text-align:left">
        <p>- Multiple components</p>
        <p>- Config changes</p>
        <p>- Filter changes - Heavy UI changes</p>
      </td>
      <td style="text-align:left">
        <p>- Back-end &amp; front-end</p>
        <p>- Complex</p>
        <p>- Full Features</p>
        <p>- Framework</p>
        <p>- Lots of testing &amp; regression</p>
        <p>- New services</p>
      </td>
      <td style="text-align:left">- Not finish-able in one sprint</td>
    </tr>
    <tr>
      <td style="text-align:left">- No other dependencies</td>
      <td style="text-align:left">
        <p>- Some dependencies</p>
        <p>- No new services</p>
      </td>
      <td style="text-align:left">
        <p>- Dependencies - New/Changed Services</p>
        <p>- Existing Integration</p>
      </td>
      <td style="text-align:left">
        <p>- Dependencies - New Integration</p>
        <p>- Security</p>
        <p>- Services</p>
      </td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>### Issue Lifecycle

For plugin projects we generally just use GitHub issues, but for client projects and internal sites we almost always use [JIRA](https://www.atlassian.com/software/jira) for issue tracking.

The following is a typical JIRA workflow, where a ticket will generally transition from start to finish, although some backtracking does happen, for instance a ticket moving to QA but then getting sent back for development.

1. **To Do**: Issue has not been worked on yet. It may or may not need to be groomed ahead of moving into development.
2. **In Development**: Issue is assigned to an engineer and work is being done.
3. **Code Review**: Engineer has finished the work and has opened a pull request, re-assigning the issue to a peer for review.
4. **QA**: Feature branch has been merged into a staging branch and is now on the staging server, and issue is assigned to testers for verification.
5. **UAT**: Issue is assigned to client for review.
6. **Queued for Deployment**: Issue has passed client review and the PR can be merged into `master`.
7. **In VIP Review**: \(WordPress.com VIP only\) Code for issue has been committed to SVN and is now in the deploy queue.
8. **Production**: Issue has been deployed. QA and client can re-verify.
9. **Done**: Issue is closed.

