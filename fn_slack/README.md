# Resilient Functions Integration to Slack

This function creates a Slack message based on a Resilient Incident, it's Tasks, Notes, Artifacts and Attachments.

Many of the features of posting a Slack message are under customer control including:
- Creating private or public channels
- Inviting users to conversations
- Preserving embedded links
- Posting messages from Incidents, Notes, Artifacts and Tasks displaying authorship
- Uploading Incident, Task or Artifact attachment
- Slack user ID <@U345GHIJKL> and channel ID #C012ABCDE references
- Exporting conversation history to a text file, saving it as an Attachment in Resilient and archiving Slack channel

## History

| Version| Comment |
| ------- | ------ |
| 1.0.2 | Support for App Host, proxy support added |

## App Host Installation
All the components for running Slack in a container already exist when using the App Host app. The remainder of this section details the Slack configuration file changes.

It's possible to override the template used for archiving a channel. 
Use the app.config setting: `template_file=/var/rescircuits/slack_template.jinja2` to reference the template file named `slack_template.jinja2` at location
`/var/rescircuits`. See the default template referenced [in the Template file section](#template_file).

## Integration Server Installation

Prerequisites:

    resilient-circuits >=v35.0.0

To install in "development mode"

    pip install -e ./fn_slack/

After installation, the package will be accessible to 
    
    resilient-circuits run

To uninstall,

    pip uninstall fn_slack

To package for distribution,

    python ./fn_slack/setup.py sdist

The resulting .tar.gz file can be installed using

    pip install fn_slack-<version>.tar.gz
    
To uninstall

    pip uninstall fn_slack
    
See the accompanying documentation for how to install to Resilient and configure the function.
    
### Configuration

1. Import the package's customization data into the Resilient Platform through the command:

    resilient-circuits customize

	This will create the following custom components:
	* Message Destinations: `slack`
	* Functions: `slack_archive_channel, slack_post_attachment, slack_post_message`
	* Action Fields: `rule_slack_channel, rule_slack_is_channel_private, rule_slack_participant_emails, rule_slack_text`
	* Custom datatables: `slack_conversations_db`
	* Workflows: `archive_slack_channel, create_slack_message, create_slack_reply, example_post_attachment_to_slack__artifact, slack_example_archive_slack_channel__task, slack_example_post_attachment_to_slack, slack_example_post_message_to_slack__artifact, slack_example_post_message_to_slack__task`
	* Rules: `Example: Archive Incident Slack Channel, Example: Archive Task Slack Channel, Example: Post Artifact Attachment to Slack, Example: Post Artifact to Slack, Example: Post Incident / Task Attachment to Slack, Example: Post Incident to Slack, Example: Post Note to Slack, Example: Post Task to Slack`                  

2. Update and edit `app.config` by first running:

		resilient-circuits config [-u | -c]. 
		
Then edit the [fn_slack]:

```
    [fn_slack]
    api_token=Your_OAuth_Access_Token
    
    # Set your bot's name that will appear as the author of the message. 
    # Must be used in conjunction with slack_as_user set to false, otherwise ignored.
    username=Resilient

    # template file override
    #template_file=/var/rescircuits/slack_template.jinja2

    # add proxy support here or use [integrations] for integration wide proxy settings
    #http_proxy=
    #https_proxy=
```

### Use

1. Start Resilient Circuits with: `resilient-circuits run`

See the accompanying documentation for how to customize and use the function.

## Template file
{%- if is_msg_parent -%}
    {{- number }} - {{ username }} POSTED ON {{ msg_time }}:{{ '\n' -}}
    {%- if msg_pretext -%}
        {{- msg_pretext }}{{ '\n' -}}
    {%- endif -%}
    {%- if msg_text -%}
        {{- msg_text }}{{ '\n' -}}
    {%- endif -%}
    {%- if file_name -%}
        {{- file_name }}{{ '\n' -}}
    {%- endif -%}
    {%- if file_permalink -%}
        {{- file_permalink }}{{ '\n' -}}
    {%- endif %}{{ '\n' -}}
    {%- if reply_count and reply_count > 0 -%}
        {{- reply_count }} REPLIES:{{ '\n' -}}
    {% endif -%}
{%- else -%}
    {{- '\t' }}{{ number }} - {{ username }} POSTED A REPLY ON {{ msg_time }}:{{ '\n' -}}
    {%- if msg_text -%}
        {{- '\t' }}{{ msg_text }}{{ '\n' -}}
    {%- endif -%}
    {%- if file_name -%}
        {{- '\t' }}{{ file_name }}{{ '\n' -}}
    {%- endif -%}
    {%- if file_permalink -%}
        {{- '\t' }}{{ file_permalink }}{{ '\n' -}}
    {% endif %}{{ '\n' -}}
{%- endif -%}