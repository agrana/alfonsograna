---
layout: post
title: "How to OSINT Yourself"
date: 2026-06-20 00:00:00 -0000
categories: [security, privacy]
tags: [osint, privacy, security, personal-security]
excerpt: "A practical checklist for auditing what the internet knows about you, from names and usernames to social media, GitHub, domains, and old documents."
author: "Alfonso Grana"
---

OSINT is not only something investigators do to other people.

It is also a useful way to understand what someone can learn about you from public sources.

1. **Define your identities**

   Start by listing the names, usernames, emails, phone numbers, domains, companies, projects, and locations that have ever been connected to you.
   Include old handles and abandoned accounts, because stale information is often easier to find than current information.

2. **Search yourself manually first**

   Search each identity in multiple search engines using quotes, variations, and combinations like name plus city, username plus project, or email plus company.
   Do this in a private window and from a logged-out browser so your own browsing history does not hide what a stranger would see.

3. **Check people-search and breach surfaces**

   Look for your name, email, phone number, and address on people-search sites, data broker pages, and reputable breach notification services.
   Do not download breach dumps; the goal is to know whether your data is exposed, rotate passwords, and request removal where possible.

4. **Audit your social media footprint**

   Review public profiles, bios, old posts, tagged photos, comments, likes, follows, and profile links across every platform you have used.
   Pay attention to small details that combine into a larger picture, such as work history, travel patterns, family names, or repeated locations.

5. **Audit GitHub and technical traces**

   Search your GitHub profile, repositories, commits, issues, package metadata, Docker images, and old project sites for emails, usernames, internal names, API keys, or infrastructure hints.
   Also check commit history, because deleting a secret from the latest version does not remove it from the repository history.

6. **Check domains you own**

   Review WHOIS records, DNS entries, certificate transparency logs, exposed subdomains, old staging environments, and forgotten redirects.
   Domains often reveal personal emails, hosting providers, project names, and services you no longer remember setting up.

7. **Check old documents and PDFs**

   Search for resumes, slide decks, school files, invoices, reports, and PDFs that include your name or username.
   Download copies you find and inspect metadata, because author fields, file paths, document titles, and creation tools can expose more than the visible text.

8. **Map your public identity graph**

   Draw links between names, accounts, emails, domains, employers, projects, addresses, and platforms so you can see how separate identities connect.
   The risk is often not one exposed fact, but the way many harmless facts make each other easier to verify.

9. **Decide what to remove, hide, or leave**

   Prioritize removing sensitive data, old addresses, phone numbers, private emails, exposed credentials, unnecessary personal details, and anything that helps impersonation.
   Leave public information only when it has a clear purpose, such as professional credibility, contactability, or ownership of work you want associated with you.
