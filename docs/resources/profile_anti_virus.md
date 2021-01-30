# Resource: valtix_profile_anti_virus

Create Anti Virus Profile

## Example Usage

### With auto updating ruleset and other defaults

```hcl
resource "valtix_profile_anti_virus" av_auto {
  name                  = "av_auto"
  description           = "description"
  action                = "DROP"
}
```

### With manually specified ruleset version

```hcl
resource "valtix_profile_anti_virus" av_manual {
  name                  = "av_manual"
  description           = "description"
  action                = "DROP"
  talos_ruleset_version = "0.103.0-01272021"
}
```

## Argument Reference

* `name` - (Required) Name of the Anti Virus profile
* `description` - (Optional) Description of the Anti Virus profile
* `auto_update` - (Optional) Auto update the Talos ClamAV Ruleset version daily with a delay specified by `delay_by_days` parameter. The valid values are true/false and it is true by default..
* `delay_by_days` - (Optional) How many days before we use a Talos ClamAV Ruleset version after it has been published by Valtix. The default for this argument is 7 days, meaning that after the Jan 1st ruleset is published by Valtix, it is applied to the profile, and hence all the gateways using the profile, on Jan 8th. Valtix publishes new rulesets every day except when our internal testing fails.
* `talos_ruleset_version` - (Optional) Talos ClamAV Ruleset version. Valid values (as of this publication of this document):
    * **0.103.0-01272021**
    * **0.103.0-01262021**
    * **0.103.0-01252021**
    * **0.103.0-01242021**
    * **0.103.0-01232021**
    * **0.103.0-01222021**

  If this argument is specified, Auto Update of Talos ClamAV Ruleset is disabled and the profile will only use this version for Talos ClamAV Ruleset.
* `action` - (Required) Default action for all the attacks. Valid values:
    * **ALERT** (logs in AV events)
    * **DROP** (drop the traffic and log the events)
    * **SILENTDROP** (drop the traffic and no log of events)
