# Agent Policies

> **This project has been archived.** The unified `devleaps-policy-client` package has been replaced by editor-specific plugins. Use the appropriate plugin for your editor:
>
> - **Claude Code**: [agent-policies-claude-code](https://github.com/Devleaps/agent-policies-claude-code)
> - **Gemini CLI**: [agent-policies-gemini-cli](https://github.com/Devleaps/agent-policies-gemini-cli)
>
> For a server reference implementation, see [agent-policies-server](https://github.com/Devleaps/agent-policies-server).

This repository now serves as documentation for the Agent Policies concept and architecture. The original `devleaps-policy-client` Python package that shipped as a single client for all editors has been superseded by dedicated per-editor plugins, which offer tighter integration and editor-specific features.

## Why Policies

### Automating Decisions

Rule files can be forgotten or ignored completely by LLMs. Policies are unavoidable:

```rego
decisions[decision] if {
	input.parsed.executable == "terraform"
	input.parsed.subcommand == "plan"
	decision := {"action": "allow"}
}

decisions[decision] if {
	input.parsed.executable == "terraform"
	input.parsed.subcommand == "apply"
	decision := {
		"action": "deny",
		"reason": "`terraform apply` is not allowed. Use `terraform plan` instead",
	}
}

```

In the image below, the agent is denied by policy from running `terraform apply`. The agent can then make decisions on what to do next, in this case it outputs that it will attempt a `terraform plan` as per the policy recommendation.

> <img width="648" height="133" alt="Screenshot 2025-10-03 at 16 15 29" src="https://github.com/user-attachments/assets/4659a391-2e96-431f-85e7-7d3973f2d101" />

### Automating Guidance

Aside from denying and allowing automatically, policies can also provide guidance through Post-* events:

```rego
guidance_activations[check] if {
	endswith(input.file_path, ".py")
	check := "comment_ratio"
}
```

In the image below, an agent makes a change which includes a comment that is detected as being potentially redundant. The guidance from the snippet is given as feedback to the change, after which the agent by itself decides to remove the redundant comment.

> <img width="592" height="427" alt="Screenshot 2025-10-21 at 21 47 37" src="https://github.com/user-attachments/assets/a63a172b-d01d-473f-a765-97b2d266df7b" />
