<!--
    DO NOT MANUALLY EDIT THIS FILE
    THIS FILE IS AUTOMATICALLY GENERATED WITH resilient-circuits codegen
-->

# McAfee ePO apply tags

## Function - McAfee Tag an ePO asset

### API Name
`mcafee_tag_an_epo_asset`

### Output Name
`None`

### Message Destination
`mcafee_epo_message_destination`

### Pre-Processing Script
```python
inputs.mcafee_epo_systems = artifact.value
inputs.mcafee_epo_tag = str(rule.properties.ss_tags)
```

### Post-Processing Script
```python
None
```

---

