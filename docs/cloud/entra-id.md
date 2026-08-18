# Case Study: Entra ID Identity Design

 Status: In Progress

## Summary

This phase of the Microsoft 365 build focused on establishing the Entra ID identity structure before adding users, groups, and additional services. The tenant was created as toshsystems.onmicrosoft.com, a consistent naming convention was chosen for user accounts, and the founding Global Administrator was secured with multi-factor authentication before any other configuration work continued.

A separate break-glass administrator was also created for emergency access. That account was intentionally left on the original onmicrosoft.com domain so it would still be usable if the custom domain became unavailable or was misconfigured. The next phase of the project will expand the directory with departmental users, groups, and role assignments for the Tosh Systems environment.

## Purpose

Identity sits underneath most of the services that will eventually be added to the tenant. Exchange Online uses user identities and addresses, Intune can assign policies and applications through group membership, and Conditional Access policies are built around users, groups, devices, and administrative roles.

Because of those dependencies, I wanted the basic account structure and administrative access model in place before building anything else. Some tenant decisions are also difficult or impossible to change later, particularly the default onmicrosoft.com domain, so naming and administrator access were handled at the beginning rather than treated as cleanup work afterward.


## Work In Progress
