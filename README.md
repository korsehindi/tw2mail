# tw2mail - send a jupyter notebook as an email

[![PyPI version](https://badge.fury.io/py/nb2mail.svg)](https://badge.fury.io/py/nb2mail)

This repo contains a `jupyter nbconvert` exporter to convert notebooks to multipart MIME, and a postprocessor to
send it via smtp.

## Installation

    pip install tw2mail

## Usage

`tw2mail` does not do anything by itself. It provides an export format
("mail") and postprocessor ("SendMailPostProcessor"). Please see the nbconvert
documentation and example configuration for more information.

## Example

To generate a mail and send it later with another process (eg `sendmail`):

    jupyter nbconvert --execute --to mail notebook.ipynb

To convert and send a mail via gmail, you can set the environment
variables and declare a postprocessor with `--post`:

    export TO=example@example.ex GMAIL_USER=user GMAIL_PASS="*****"
    jupyter nbconvert --to mail --post=nb2mail.SendMailPostProcessor notebook.ipynb

Alternatively, you can configure the SMTP settings in a config file `config.py`:

    c = get_config()
    c.NbConvertApp.export_format = 'mail'
    c.Exporter.preprocessors = 'nbconvert.preprocessors.ExecutePreprocessor'
    c.NbConvertApp.postprocessor_class = 'nb2mail.SendMailPostProcessor'
    c.SendMailPostProcessor.recipient = 'example@example.ex'
    c.SendMailPostProcessor.smtp_user = 'user'
    c.SendMailPostProcessor.smtp_pass = '*******'
    c.SendMailPostProcessor.smtp_addr = 'smtp.gmail.com'
    c.SendMailPostProcessor.smtp_port = 587

and then run:

    jupyter nbconvert --config config.py demo.ipynb

## Configuring Mail Headers

In the notebook metadata, you can set mail headers by adding a `nb2mail` block:

    "tw2mail": {
    "attachments": [
        "business_report_attachment.xlsx"
    ],
    "From": "reports@example.com",
    "To": "person1@example.com, person2@example.com",
    "Subject": "Business Report"
    }

You can specify multiple recipients by seperating them with commas.

## Disabling Pilcrows

Since CSS doesn't render the same in email, you may want to disable the pilcrows after each section.

    c.MailExporter.anchor_link_text = '' # disable pilcrow, requires nbconvert >= 5.2
