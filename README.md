# Introduction
[AWS Lambda Layers](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html) allow you to package and distribute function code dependencies across multiple functions without needing to zip the dependencies in each [function package](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html) you create. Tenable provides a Python library called [pyTenable](https://pytenable.readthedocs.io/) for interacting with the Tenable API. You can use pyTenable to automate your vulnerability management workflows through calls to the Tenable API. Finally, [Github Actions](https://github.com/features/actions) provide a framework for automating build and deployment steps when creating software.

# pyTenable Lambda Layer Github Action
This Github Action is a manually triggered build and deployment of a pyTenable Lambda Layer that gets uploaded to every supported AWS region within your account. 
