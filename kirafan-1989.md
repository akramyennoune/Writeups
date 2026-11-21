TRACING kirafan1989
TryHackMe OSINT room: Writeup
author: akrammmm
Platform: TryHackMe
Date: 20 November 2026

Summary: The investigation starts from one username and ends at a hidden
draft. Along the way it passes through a GitHub profile, an archived blog
post, a photo with GPS metadata, a leaked dataset and an unlinked page. For
each task I note how I found the answer and how much I trust it.


=====================================================================
TASK 2: Username expansion
=====================================================================
Goal: Start from the username kirafan1989, find related public profiles and
judge how reliable they are.

Q: What is the username under investigation?
A: kirafan1989

Q: Which website hosts a profile for this username?
A: Github

Q: What is the name of the blog linked from the profile?
A: Apples & Ink

Q: Two profiles match the username pattern. What is the other username?
A: kirafan1998

Q: Which of the two is the real target?
A: kirafan1989

Method:
I searched for the username on GitHub and across platforms, then opened each
hit by hand to rule out false positives. A GitHub user search for "kirafan"
returns 13 users. One of them, kirafan1998, differs from the target only by
the last two digits being swapped.

The kirafan1989 profile has the bio "collector. writer at Apples & Ink". Its
only repository, kirafan1989.github.io, hosts the blog. The account joined 7
hours before my screenshot and has 7 commits, all in that repository.

I also ran the username through IDCrawl. It lists a Twitter profile ("kira -
@kirafan1989", Death Note avatar, location "somewhere around the world") and
the GitHub profile. The other sites came back as unknown. IDCrawl results are
automatic, so I treated them as supporting evidence only.

Reliability:
kirafan1989 is the real target. Its bio, blog and repository match the
Apples & Ink context, the blog's contact email matches a row in the leaked
data (Task 6), and the photo metadata matches the blog date (Task 4).
kirafan1998 only shares the naming pattern and has no link to the blog.

<img width="959" height="539" alt="apples" src="https://github.com/user-attachments/assets/20f1ec9a-9824-4c87-9fbe-1503610b652b" />

<img width="960" height="540" alt="idcrawl" src="https://github.com/user-attachments/assets/9c8713bb-9dbc-4fc4-b97c-9743fa50f88f" />




=====================================================================
TASK 3: Archived blog post
=====================================================================
Goal: Compare an archived version of a blog post with the current one and
recover what was removed.

Q: What is the title of the blog post?
A: Notes on Waiting

Q: What date was the post published?
A: March 14, 2026

Q: What email address appeared on the archived version of the post?
A: ryuk.apples.1989@gmail.com

Q: What service did you use to view the old version?
A: Wayback Machine

Method:
The blog is a GitHub Pages site. On index.html, the "Latest post" link goes to
post1.html, the post "Notes on Waiting". I submitted that URL to the Wayback
Machine, which has one capture, from 19 November 2026. The archived page says
"Posted March 14, 2026" and ends with "Contact: ryuk.apples.1989@gmail.com".
That email is no longer on the live page.

URL used:
  https://kirafan1989.github.io/post1.html

<img width="959" height="326" alt="Capture d&#39;écran 2026-11-19 222611" src="https://github.com/user-attachments/assets/b3b910cd-cc9d-4985-a945-ac8276810f51" />



=====================================================================
TASK 4: Hidden in the pixels
=====================================================================
Goal: Read the metadata of a photo and follow its GPS tags.

Q: What camera model took the photo?
A: Canon EOS M50

Q: What date and time was it taken?
A: 2026:03:14:18:42:07

Q: What tool did you use to read the metadata?
A: ExifTool

Q: The GPS tags point to which Tokyo district?
A: Akihabara

Method:
I loaded the photo (shelf-1795104698924.jpg, 1200 x 1600 JPG) into the
exif.tools web viewer, which reads the file's ExifTool metadata. It showed
the camera model, the capture time (2026-03-14 18:42:07) and saved GPS
coordinates, 35.698400, 139.773100. On the map the pin sits in Chiyoda City,
in the Akihabara electronics district. The photo shows a shelf of Death Note
figures, which fits the collector bio.

The capture date, 14 March 2026, is also the date the blog post was
published, so the photo ties back to the blog.

<img width="960" height="540" alt="exif" src="https://github.com/user-attachments/assets/91c6b1ad-1c96-4086-9386-7acc8c417f1f" />
<img width="960" height="540" alt="gps" src="https://github.com/user-attachments/assets/2f773a5f-db8f-4b5f-a8fb-67b4e959810a" />




=====================================================================
TASK 5: View from the town
=====================================================================
Goal: Every tag has been stripped from the second photo, so only what is
visible in the frame can be used.

Q: What is the nearest station?
A: JR Akihabara Station

Q: What technique or tool is the most efficient here?
A: Reverse image search

Method:
A Yandex reverse image search returned many near-identical photos of the same
street corner and suggested "Akihabara Electric Town". To find the exact
corner, I compared the buildings with Google Maps Street View (Tokyo
Prefectural Route 437, Chiyoda City). The yellow and orange buildings with
the large signs matched the photo, and the nearest station on the map is
Akihabara.

<img width="960" height="540" alt="yandex" src="https://github.com/user-attachments/assets/0b6dbd2c-ec1f-4c57-9a1d-6f7a221b7d7a" />

<img width="960" height="540" alt="streetview" src="https://github.com/user-attachments/assets/f33efb5f-aae7-4191-b03e-d7be7d4388e6" />



=====================================================================
TASK 6: The leak
=====================================================================
Goal: A data dump of 200 rows has surfaced. Find the handle kirafan1989 in it
and match the patterns.

Q: How many rows contain the username kirafan1989?
A: 1

Q: What is the real name in that row?
A: Haruto Mizuno

Q: Which company is listed?
A: Kyomu Trading Co.

Q: What password was leaked?
A: Ryuk2008!

Method:
The file leak-1795105508143.csv has the columns username, email, password,
name and company. Excel showed broken characters because of an encoding
problem, so I used the OpenBrowserTools CSV Search tool instead. Searching
for kirafan1989 returned exactly 1 row:
  username: kirafan1989
  email:    ryuk.apples.1989@gmail.com
  password: Ryuk2008!
  name:     Haruto Mizuno
  company:  Kyomu Trading Co.
The email in this row is the one from the archived blog post, which confirms
that the row belongs to the target.
<img width="960" height="485" alt="csv" src="https://github.com/user-attachments/assets/d437c621-df61-4fa0-b074-62e2c47b04c5" />
<img width="960" height="540" alt="csvsearch" src="https://github.com/user-attachments/assets/aa4e7f69-b2c8-4743-ae0d-eab1d6041708" />




=====================================================================
TASK 7: The last memo
=====================================================================
Goal: A draft was left unlinked and encoded. Sites sometimes tell crawlers
what to avoid, and that list shows where to look.

Q: What is the final flag?
A: THM{akihabara_december_05}

Method:
The blog repository contains draft.html, a page that nothing links to. It is
titled "Untitled draft", says "Last saved March 14, 2026. Not published." and
holds this encoded string:
  TWVldCBhdCBBa2loYWJhcmEgU3RhdGlvbiwgRGVjZW1iZXIgNSwgMTk6MDAu
I pasted it into CipherPad, which unwrapped it in one layer as Base64 (no
key) and returned:
  Meet at Akihabara Station, December 5, 19:00.
That message gives the flag.
[TODO: say how you found draft.html (robots.txt entry or the repo file list)]

<img width="960" height="540" alt="draft" src="https://github.com/user-attachments/assets/18a5b3d4-ab6a-459a-ad64-b59e4c899b96" />

<img width="960" height="540" alt="draft" src="https://github.com/user-attachments/assets/e8d6a119-2b91-4cfc-b934-02805be1aff0" />



=====================================================================
TASK 8: Conclusion
=====================================================================
Every step in this room used public information: a username, an archived
page, photo metadata, a leaked file and an unlinked page. No single piece
identified the person, but the email, the photo date and the blog date all
pointed to the same account once I put them side by side.


=====================================================================
TOOLS USED
=====================================================================
Profile analysis:  GitHub, IDCrawl (profiles, bio, repositories)
Past versions:     Wayback Machine (recover removed content)
Metadata:          exif.tools, ExifTool (camera, date, GPS tags)
Image origin:      Yandex Images (reverse image search)
Location check:    Google Maps and Street View (confirm the spot and station)
Leaked data:       OpenBrowserTools CSV Search (search the leaked CSV)
Encoded text:      CipherPad (decode the draft, Base64)


=====================================================================
TAKEAWAYS
=====================================================================
- A matching username proves little. Check the bio, blog and dates before
  calling a profile the target. kirafan1998 only looks similar.
- Deleted content is often still available in web archives.
- Stripped metadata can still be worked around: signs and buildings in the
  frame can be matched with reverse image search and Street View.
- The email, the photo date and the blog date all point to the same person.
- Reused handles and predictable passwords (Ryuk2008!) make it easy to link a
  leak to a real identity.
- Unlinked pages can expose content that was never meant to be public, and
  Base64 is encoding, not protection.
- All data used here was public, and investigations like this should stay
  within legal and ethical limits.
