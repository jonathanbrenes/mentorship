# Linux Recovery Lab – Production-Grade Node Stabilization in Azure

## Scenario

A cloud-based Linux environment has been provisioned in Azure using infrastructure-as-code. The deployment introduces operational instability on one node to simulate a realistic post-deployment production incident.

Initial observations indicate:

- One node appears stable and responsive.
- A second node exhibits inconsistent boot or startup behavior.
- Symptoms may vary across reboots.

This lab reflects real-world scenarios where post-deployment configuration changes, automation drift, or service constraints introduce instability in a production system.

Redeployment is not permitted.

The objective is to restore predictable and repeatable system behavior while maintaining operational discipline.

---

## Deployment

Deploy the lab environment using the ARM template below:

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Frecovery001.json)

Ensure provisioning completes before initiating analysis.

---

## Skills Required

This exercise assumes experience consistent with senior Linux engineers or SRE practitioners:

- Deep understanding of Linux boot sequencing and service initialization
- Familiarity with systemd service lifecycle behavior and dependency modeling
- Structured log analysis across multiple boot cycles
- Comparative configuration analysis between healthy and degraded systems
- Experience with enterprise diagnostic tooling (e.g., `sos report`, `supportconfig`)
- Familiarity with rescue or repair workflows in cloud environments
- Operational discipline in incident handling and validation
- Ability to distinguish symptoms from root causes without guess-driven changes

This lab emphasizes systemic reasoning over isolated troubleshooting.

---

## Recommended Prerequisites

Participants should be comfortable with:

- Multi-node Linux environments
- System-level diagnostics and boot-chain analysis
- Enterprise support archive generation (`sos report` for RHEL, `supportconfig` for SLES)
- Reviewing boot diagnostics and serial console output in Azure
- Conceptual rescue VM workflows for disk-level inspection
- Structured incident documentation

---

## Objectives

By completing this lab, you should be able to:

- Diagnose non-deterministic startup instability in a cloud-hosted Linux system.
- Establish and use a known-good baseline for differential analysis.
- Apply structured incident-response methodology without resorting to redeployment.
- Validate stability through repeatable and measurable criteria.
- Demonstrate persistence verification across multiple restart cycles.
- Produce production-grade documentation aligned with real incident reporting standards.

---

## Environment Overview

- Azure-based virtual machine deployment
- RHEL- or SLES-compatible image
- Shared network topology
- Boot diagnostics enabled
- Potential system-level modifications introduced post-deployment
- Rescue VM workflows available if direct boot is impaired

Constraints:

- No redeployment
- No permanent weakening of security controls
- No undocumented configuration changes
- Recovery must persist across multiple reboots

One node may be treated as a reference baseline for comparison.

---

## Your Mission

Restore the unstable node to a stable, supportable, and repeatable operational state.

Stability is defined as:

- Consistent and deterministic boot behavior
- Reliable SSH access
- No recurring critical service failures
- No spontaneous or unexplained reboots
- Clean startup state across repeated restart cycles
- Absence of recurring high-severity log anomalies

Recovery must be validated through disciplined observation and repeated verification.

---

## Analytical Guidance

Approach this as a production incident rather than a configuration exercise.

Focus on systemic evaluation across domains such as:

- Boot-chain sequencing and initialization flow
- Service dependency resolution and startup constraints
- Resource allocation and service limits
- SSH initialization timing and service state transitions
- Kernel runtime context and persistent configuration influence
- Automation artifacts and initialization scripts
- Storage and mount dependencies
- Cross-node configuration drift
- Reboot consistency patterns

When direct system access is impaired, consider:

- Azure serial console diagnostics
- Boot diagnostics review
- Conceptual rescue VM workflows for offline inspection
- Generating enterprise support archives (`sos report` or `supportconfig`) from rescue context for comparative analysis

Maintain an evidence-first discipline:

1. Observe before modifying.
2. Form hypotheses grounded in collected data.
3. Apply minimal, controlled changes.
4. Reboot and re-evaluate.
5. Confirm persistence across additional cycles.

Avoid reactive or assumption-driven changes.

---

## Validation Criteria

Recovery is complete only when:

- Multiple consecutive reboots demonstrate consistent behavior.
- SSH availability is reliable and predictable.
- No recurring critical service failures appear post-boot.
- System logs show no repeated high-severity anomalies across cycles.
- Behavior aligns with expected baseline characteristics.
- Stability persists beyond a single verification cycle.

Single-instance success does not constitute resolution.

---

## Documentation Expectations

Produce a structured, production-grade incident report including:

### 1. Executive Summary  
High-level description of symptoms and final operational state.

### 2. Environment Context  
Infrastructure overview and operational constraints.

### 3. Observed Behavior  
Pattern-based description of instability.

### 4. Evidence Collected  
Boot logs, service state analysis, diagnostic archives, cross-node comparisons.

### 5. Investigative Reasoning  
Hypotheses evaluated and validation strategy (conceptual).

### 6. Stabilization Overview  
High-level description of corrective direction taken.

### 7. Stability Verification  
Repeatable validation results and consistency metrics.

### 8. Preventive Considerations  
Operational safeguards and process improvements to reduce recurrence.

Clarity, traceability, and reasoning discipline are expected.

---

## What Not To Do

- Do not redeploy the infrastructure.
- Do not apply configuration changes without evidence.
- Do not disable security controls to regain access.
- Do not rely on a single successful reboot.
- Do not modify multiple variables simultaneously without isolation.
- Do not treat symptoms as root causes without validation.

Operational integrity is part of the evaluation.

---

## Real-World Context

Production instability frequently arises from:

- Post-deployment configuration drift
- Automation side effects
- Service limit misconfiguration
- Incomplete initialization workflows
- Kernel or boot parameter misalignment
- Improper hardening steps
- Dependency ordering inconsistencies
- Partial recovery actions

In distributed environments, multiple system layers may interact, creating non-deterministic failure patterns.

Senior engineers must reason across these layers rather than focus on isolated error messages.

---

## Optional Advanced Exploration

After restoring stability, consider:

- How would this condition impact quorum or cluster-level behavior?
- What monitoring signals would provide early detection?
- How could automation pipelines validate post-deployment integrity?
- What observability improvements would reduce time-to-diagnosis?
- How could support archive analysis be automated for baseline comparison?
- What safeguards could prevent similar drift in future deployments?

These considerations extend the exercise from recovery into reliability engineering and operational resilience.

---

This lab is designed to mirror real production instability patterns and reinforce disciplined, repeatable recovery practices expected of senior Linux and SRE professionals.
