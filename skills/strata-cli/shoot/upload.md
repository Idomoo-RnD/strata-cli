# Uploading a generation input

Canonical publication policy (**PRIVACY-001**). The upload store is public, permanent and has no
delete. A temporary use does not make the publication temporary. This applies to direct upload
and automatic caption re-hosting. [Evidence and tests](../qa/evidence-index.md).

## `strata upload <file>` — ONLY for generation inputs with no URL

### 🔴 CRITICAL — the whole rule in one line

Upload only a **non-sensitive input without a usable existing URL**, for a specific generation
call, after informed consent to permanent public publication. Caption hosting is the other
consumer and follows the same privacy gate. Never publish private, personal, customer-personalized
or client-confidential material to this store—even if it would make the command convenient.

`--allow-public-upload` records explicit consent for the particular non-sensitive input. It is
not permission to ignore the policy. Unattended mode, a stored library choice, or a generic request
to make a video does not supply that consent. Without the flag the CLI refuses direct upload and
caption re-hosting before publishing. Prefer already hosted permitted inputs when available.

### 🛑 The two-condition test

1. Is hosting necessary for a specific URL-only generation input (or captions), with no usable
   existing URL and no local/base64 path accepted by that command?
2. Is the asset non-sensitive and has the user approved the irreversible public publication?

Both must be true. Record the asset, intended consumer and approval. If not, use a permitted local
route, ask for an approved compatible URL, or stop and explain the blocker.

### Most generation inputs already have a URL

`generate image`, `edit image`, `generate video`, narration/music/avatar and `render` normally
return hosted URLs. Capture them; do not upload another public copy. Their local files are for
scene embedding and editing. Image-reference and image-edit commands can encode local files
without this upload store; they still send inputs to an external AI service, which also needs
appropriate data-use permission.

| Input | Action |
|---|---|
| Already hosted approved generation asset | Reuse its URL |
| Non-sensitive local generation reference | Ask for informed publication consent; upload only if approved |
| Local/non-S3 caption input | Same consent gate before the CLI re-hosts it |
| Existing compatible S3 caption URL | Use without a second upload, subject to data-use permission |
| Private/customer photo or personalized data | Do not publish here; require an approved hosting/processing route |
| Scene asset, font, final deliverable or brand document | Keep in the project; do not upload for storage |

### 🛑 TEMPORARY assets only — never persistent ones

“Temporary” describes the purpose of a permitted handoff, **not** retention. The store does not
expire or delete the object. It is not a project asset library or a destination for deliverables.
Scene `src` assets are embedded from local files in the IDM; publication is unnecessary for them.

**Render-time personalized media is a different workflow.** It needs renderer-reachable media
URLs under an approved data policy, not this public upload service for customer files. Confirm
access and expiry behavior with the actual renderer before promising a signed/private-URL route.
See [personalization.md](../engine/personalization.md).

### When a URL is actually required

Video first/last frames and video reference inputs take URLs. Use an existing approved generated
URL where possible. Consult current command help for avatar/image input forms; do not upload just
because a generic example used a URL. Captions can pass through compatible S3 URLs, rewrite an
existing t.idomoo.com object address, or re-host other inputs only after the consent gate.

```bash
# Only after informed consent, for a non-sensitive reference:
strata upload approved_reference.png --allow-public-upload
# Captions on an approved existing compatible URL avoid re-hosting:
strata captions "<approved-S3-url>" -o cues.json
```

### ⚠ Public and permanent — which is why it is for temp use only

The endpoint is unauthenticated and public-read, with no expiry or delete. Warn before publication,
not just after it. If consent or sensitivity is uncertain, stop. Retain the resulting URL so a retry
does not create another copy. Do not present a hosted URL as evidence that an asset was safe to publish.

### The extension must match the bytes

The host sniffs the first 261 bytes and can reject an extension mismatch with an empty 404. The
CLI detects the real type and reports a rename; a `.png` filename alone does not prove PNG bytes.
The CLI also verifies the resulting URL serves. A served URL proves availability, not privacy.
