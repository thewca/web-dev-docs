---
title: Competition Form
parent: Knowledge Base
layout: default
---

The competition form has 2 components:
1. React frontend
2. Rails backend

To create a competition, the frontend sends a POST request to `/competitions`, which is handled by the `create` method in `competitions_controller.rb`. This is one of the default routes created by `resources :competitions` in `routes.rb`, which is why it is not explicitly defined in that file.

## Adding a Field to the Competition Form

1. If necessary, create and run a migration to add it to the Competition model
    1. Add the field(s) to cloneable/uncloneable attributes
    2. Add the field(s) to the dev database dumper
    3. Add any validations needed for these fields - strongly recommended to add tests against these validations as well
2. Make the fields available in the competition form by field names to:
    1. `competition.rb#to_form_data`
    1. `competition.rb#form_data_json_schema`
    2. `competition.rb#form_errors`
    3. `competition.rb#form_data_to_attributes`
3. Add the fields to the competition form by:
    1. Locating the relevant component in `app/webpacker/components/CompetitionForm/FormSections`
    2. Create fields by analzying and copying/editing the existing fields
    3. Ensure that the necessary keys are created in en.yml (viewing the competition form where your fields have been added will tell you which keys are missing - these can take a while to update)
