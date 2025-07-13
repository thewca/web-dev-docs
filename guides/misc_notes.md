---
title: Creating Competitions With Database Entries
parent: Guides
layout: default
---

## Creating Competitions With Database Entries

1. Create an entry in the Competitions table
    1. Find an existing competition in the database that you want to use as a reference/basis and export it as CSV
    1. Make changes to the CSV as required
    1. Remove the headers from the CSV and save it
    1. When in the Competition table in phpmyadmin, click "Import" and upload the file
2. Create delegate report entries for the relevant comp
    1. In the delegate reports table, export the row relating to your template competition
    1. Edit the CSV to have the correct competition_id, and duplicate the row for any additional comps you created
    1. Run "SELECT MAX(id) FROM `delegate_reports`" in phpmyadmin to get the max id of the table - make sure that your id columns start from the MAX(id)+1 and increment
    1. Import to the delegate_reports table
3. Add competition_events (similar process to creating delegate reports)
4. If needed - add competition_organizers and competition_delegates, again using a similar process to the delegate_report imports
