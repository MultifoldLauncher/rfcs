# multifold.instance.json

## Summary

This document acts as a Request for Comments (RPC) on the topic of how a MultiFold instance metadata is formatted.

## Motivation

This RFC describes a scalable, portable, and more powerful format for the instance that defines what an instance's
definition should look like. MultiFold offers users the ability install mod loaders and mods seamlessly, without manual
configuration.

## Explanation

The following is the schema for instance metadata. The primary format is JSON, however it can be represented in other
formats such as YAML.

<details>
<summary>apiVersion: <code>string</code></summary>

Represents the API version being used. Current supported API versions: `v1alpha1`.
</details>

<details> 
<summary>kind: <code>string</code></summary>

Represents the kind of the API object being used. In this instance, `Instance`.
</details>

<details> 
<summary>metadata: <code>InstanceMetadata</code></summary>

Contains metadata for the current instance, such as display name and group name.
</details>

<details> 
<summary>spec: <code>InstanceSpec</code></summary>

Contains spec for the current instance, such as Minecraft version, mod loader, and mods.
</details>

### InstanceMetadata

<details> 
<summary>name: <code>string</code></summary>

The display name to use for the instance.
</details>

<details> 
<summary>group: <code>string?</code></summary>

The group name to group the instance with.
</details>

### InstanceSpec

<details> 
<summary>components: <code>InstanceComponent[]</code></summary>

Array of "components" in the instance. This is used to describe the launching pipeline, and is order-sensitive.
</details>

<details> 
<summary>mods: <code>InstanceMod[]</code></summary>

Array of mods in the instance. This is used to automatically update mods in the instance and compatibility checks.
</details>

## InstanceComponent

<details> 
<summary>id: <code>string</code></summary>

The id of the component. This must match a component in MultiFold, otherwise the instance will not launch.
</details>

<details> 
<summary>version: <code>string</code></summary>

The required version of the component. It is recommended to follow [semver](https://semver.org/) specs. It should be
possible to define a "version range", similar to dependencies in `package.json`. Read more about version
ranges [here](https://docs.npmjs.com/cli/v6/using-npm/semver).
</details>

<details> 
<summary>settings: <code>Record&lt;string, string&gt;?</code></summary>

Record or a map of settings. This is optional and can be freely defined. The options will be passed to the component
upon initialization.
</details>

## InstanceMod

<details> 
<summary>id: <code>string</code></summary>

The id of the mod **from the provider**. This is not to be confused with the mod id defined in mcmod.info or
fabric.mod.json.
</details>

<details> 
<summary>provider: <code>string</code></summary>

The provider id of the mod. This is the source that MultiFold retrieves the mod from. This should match a content
provider id registered in MultiFold, otherwise the instance will not launch.
</details>

<details> 
<summary>version: <code>string</code></summary>

The required version of the mod. It is recommended to follow [semver](https://semver.org/) specs. It should be possible
to define a "version range", similar to dependencies in `package.json`. Read more about version
ranges [here](https://docs.npmjs.com/cli/v6/using-npm/semver).
</details>

## Example

```json
{
  "apiVersion": "v1alpha1",
  "kind": "Instance",
  "metadata": {
    "name": "Fabric 1.17.1",
    "group": "Fabric"
  },
  "spec": {
    "components": [
      {
        "id": "java",
        "version": "^16",
        "settings": {
          "memoryInit": "1G",
          "memoryMax": "1G"
        }
      },
      {
        "id": "net.minecraft.client",
        "version": "1.17.1"
      },
      {
        "id": "net.fabricmc",
        "version": "0.11.6"
      }
    ],
    "mods": [
      {
        "id": "sodium",
        "provider": "com.modrinth",
        "version": "mc1.17.1-0.3.2"
      }
    ]
  }
}
```

## Drawbacks

The primary drawback of the proposed format is that it has not been adopted by other launchers (such as MultiMC and
GDLauncher). Creating a custom format will require a migration tool for the best possible user experience.

## Rationale and Alternatives

There is no good alternatives to creating a custom format, due to the nature of how MultiFold is designed.

## Prior Art

The specification is inspired by MultiMC's `mmc-pack.json` format.

The object design is inspired by Kubernetes' object format.

## Unresolved Questions

- How will MultiFold handle "unmanaged" mods?