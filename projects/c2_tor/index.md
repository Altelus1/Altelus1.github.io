---
title: C2 Over TOR
layout: page
type: project
meta: Proof of Concept - C2 Over TOR
---

# C2 Over TOR

### Introduction
This project is about setting up a _Command and Control_ as a hidden service in TOR network. An onion link is embedded in an agent or "malware" that constantly connects to that hidden service. The C2 itself is a simple flask server that sets the command to execute in the HTTP header and then expects the output from the agent. The output is just simply dumped in a file.


### Architecture

#### The Agent

#### The Command and Control Server

### Summary

**Sources:**
- https://github.com/cretz/bine
- https://flask.palletsprojects.com/en/2.0.x/
- https://pkg.go.dev/net/http


