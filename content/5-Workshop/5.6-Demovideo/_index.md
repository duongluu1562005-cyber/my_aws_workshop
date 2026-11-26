---
title: "APPLICATION VERTIFICATION AND DEMO VIDEO"
date: 2026-11-19
weight: 6
chapter: false
pre: " <b> 5.6 </b> "
---
After deploying the six stacks, running bootstrap, migrations and seed, and switching Edge to live, verify Tindy through its web interface. This section follows a Backend Developer completing a profile, creating a project, finding opportunities and teammates, exchanging project messages, and enriching a profile from a PDF CV.

## Demo video

Recorded on **November 23, 2026**, the video runs approximately **7 minutes 22 seconds**. Two browser windows provide separate sessions for member and leader interactions. Use full-screen playback to read forms and messages.

Tindy — application demonstration on AWS

Tindy — application demonstration on AWS

<video width="100%" controls preload="metadata">
  <source src="/my_aws_workshop/images/5-Workshop/5.6-Demovideo/video-demo.mp4" type="video/mp4">
  Your browser does not support video playback.
</video>

[Watch the demo video](/FCAJ-Worklog/images/5-Workshop/5.6-Demovideo/video-demo.mp4)


### Viewing guide

These approximate timestamps locate the main stages of the recording.

| **Time**    | **Content**                                                        |
| :---------- | :----------------------------------------------------------------- |
| 00:00–01:50 | Login sessions and Backend Developer onboarding.                   |
| 01:50–03:05 | Create and publish Campus Study Hub [Demo].                        |
| 03:05–04:00 | Discover projects, apply, and review candidates as leader.         |
| 04:00–04:40 | Exchange project messages between two sessions.                    |
| 04:40–06:00 | Find teammates, send invitations and observe Accepted status.      |
| 06:00–06:25 | Project chat and switching to AI Studio to select a PDF CV.        |
| 06:25–07:22 | AI Studio scanning, extracted suggestions and the updated profile. |

## 1. Prepare the demo sessions

Open the deployed CloudFront hostname. This session uses `https://d1vcn4g2ifl3k8.cloudfront.net`. For another environment, obtain `DistributionDomainName` from Edge Outputs.

Use a new account for onboarding and a seeded demo account in a separate browser session. Seeded accounts already completed onboarding and can act as application reviewers, invitation recipients and chat participants. Use sample data for the write operations in this exercise.

Before recording, readiness through CloudFront returned HTTP 200 Healthy, ECS ran one API task, and four CloudWatch alarms were recorded as OK. Sections 5.4 and 5.5 contain infrastructure verification evidence.

## 2. Onboard a Backend Developer

Complete Basic information, Technical skills, Preferred role, Availability, Interests & Goals, Projects & experience, GitHub & Links, and Review & complete profile.

| **Information**      | **Practice**                                                                       |
| :------------------- | :--------------------------------------------------------------------------------- |
| Basic information    | Enter display name, username and university/community.                             |
| Skills               | Select relevant skills such as .NET, C#, ASP.NET Core, Docker, PostgreSQL and Git. |
| Role                 | Choose Backend Developer.                                                          |
| Availability         | Enter weekly participation hours.                                                  |
| Goals and experience | Describe REST API/database experience and team learning goals.                     |
| Review               | Check the profile before completing onboarding.                                    |

Profile data introduces the member’s background and supplies matching inputs. The Discovery scores demonstrated later are **Rule-based match** results.

![Selecting onboarding skills](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-50.jpg)

## 3. Create a project and recruitment position

Open **Leader Workspace → Create project**. The video creates `Campus Study Hub [Demo]`, adds a description, selects Web Application and configures a Frontend Developer position using React/TypeScript.

The wizard covers **The big picture → People & skills → Commitment & review**. Enter project information, position requirements, commitment and recruitment deadline, then review and publish. The recorded review shows one opening, 10 hours/week, a three-month duration and a maximum team size of five.

After saving, the project appears as recruiting in Leader Workspace, ready to receive applications and support teammate discovery.

![Created project in Leader Workspace](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-180.jpg)

## 4. Discover projects and review applications

In **Discover Projects → Find Projects**, inspect project cards, open positions and match scores. Open project details or matching explanations before applying.

The video submits an application to `Learning Roadmap Board [Demo]` for Backend Developer with a message. In the leader session, Candidate applications shows the applicant and **Accept to team** action. After application handling, both users appear in project chat.

The score in the recording is an example, not a fixed expected value for every user. Results depend on profiles, positions, availability and current data.

![Leader reviewing an application](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-230.jpg)

## 5. Exchange project messages

Open **Messages → Project chats** and select a project shared by both accounts. Send a message from one session, observe it in the other window and reply to test both directions.

The recording shows two-way messaging in `Learning Roadmap Board [Demo]`. After teammate acceptance, `Campus Study Hub [Demo]` also shows member events and project messages. This is UI behavior evidence; the recording does not separately show a DevTools WebSocket 101 response.

![Two sessions exchanging project messages](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-250.jpg)

## 6. Find teammates and send invitations

Switch to **Find Teammates**, select a project you lead and its open position. The recording uses `Campus Study Hub [Demo]` and Frontend Developer. Candidate cards show skills, availability and Position Match to support the leader’s decision.

Follow the shortlist/invitation flow for a suitable candidate. The recipient opens **My Projects → Invited** to review and respond. The recording shows an **Accepted** invitation, member join events and the team’s project chat.

![Frontend Developer candidate matching](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-310.jpg)

![Accepted invitation and project chat](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-350.jpg)

## 7. Additional practice: certificates and attachments

Practice this separately under **Profile → Certificate**. Enter the certificate title, issuer and issue date, then select an appropriate attachment. Label self-created sample certificates as Demo. The inspected video frames do not establish completion of the certificate flow; the PDF clearly shown near the end is an AI Studio CV.

Distinguish saving certificate metadata from attachment processing. Confirm readiness only after the interface reports the appropriate state and the document can be viewed. File selection alone does not establish a successful malware scan.

## 8. AI Studio: PDF CV to profile suggestions

Open **AI Studio** and select a CV under **CV (optional)**. The current interface supports PDFs up to 2 MiB and 10 pages. Wait for scanning to complete, then select **Extract suggestions**.

In the video, `CV_Backend.pdf` reaches **Scan complete. Ready to extract.**. Review suggestions displays skills and the Experience and certificates and Extraction notes panels. The user selects relevant suggestions, saves, and opens the profile to inspect Technical skills.

Review extracted information before saving it. Discovery Rule-based match scores and AI Studio extraction are separate features.

![Scanned CV and extracted suggestions](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-410.jpg)

![Profile at the end of the recording](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-435.jpg)

## Results and verification scope

| **Flow**     | **Recorded evidence**                                                                            |
| :----------- | :----------------------------------------------------------------------------------------------- |
| Profile      | Onboarding and workspace access in an authenticated session.                                     |
| Project      | Project creation and listing in Leader Workspace.                                                |
| Application  | Application submission, leader review and acceptance action.                                     |
| Teammates    | Matching, invitations, Accepted status and members in chat.                                      |
| Chat         | Messages displayed between two sessions in project chat.                                        |
| Certificates | Additional practice instructions; completion is not established by the inspected video segments. |
| AI PDF       | Completed scan, extracted suggestions and profile after updating.                                |

After verification and evidence collection, follow [5.7 Clean up resources](https://mjoy1410.github.io/FCAJ-Worklog/5-workshop/5.7-cleanup/) according to the operating plan.
