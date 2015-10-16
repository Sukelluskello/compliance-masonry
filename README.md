# Control Masonry - Alpha

## About
Control Masonry allows users to construct certification documentation, which is required for approval of government IT systems and applications.

Alpha Note: Control Masonry is an emerging project. We recognize that in its current state, Control Masonry's user experience still needs to mature. Nevertheless, we are "eating our own dog food" and working to make continuous improvements.

#### Long Term Plan Diagram
![control-masonry](https://cloud.githubusercontent.com/assets/47762/9829499/08d2b1dc-58bb-11e5-8185-5dc617188ae7.png)
(Here's [the .gliffy source](https://gist.github.com/mogul/8d7cb123e03b0fe1b993).)

## Benefits
Modern applications are build on existing systems such as S3, EC2, and Cloud Foundry. Documentation for how these underlying systems fulfill NIST controls or PCI SSC Data Security Standards is a prerequisite for receiving authorization to operate (ATO). Unlike most [System Security Plan documentation](http://csrc.nist.gov/publications/nistpubs/800-18-Rev1/sp800-18-Rev1-final.pdf), Control Masonry documentation is organized by components making it easier for engineers and security teams to collaborate.

Control Masonry simplifies the process of certification documentations by providing:
1. a data store for certifications (ex FISMA), standards (ex NIST-800-53), and the individual system components (ex AWS-EC2).
2. a way for government project to edit existing files and also add new control files for their applications and organizations.
3. a pipeline for generating clean and standardized certification documentation

# Creating Documentation (How to use)
### Data Flow Diagram
![control_masonry](https://cloud.githubusercontent.com/assets/4596845/10542998/e6397422-73e9-11e5-8681-5539be8b8164.png)

### Adding Data
Data can be added via two potential entry points. The [`data/xlsx/Control-Masonry.xlsx`](https://github.com/18F/control-masonry/blob/master/data/xlsx/Control-masonry.xlsx) document or the [`data/components`](https://github.com/18F/control-masonry/tree/master/data/components) directory.

[**OPTION 1: Control-Masonry.xlsx**](https://github.com/18F/control-masonry/blob/master/data/xlsx/Control-masonry.xlsx)
Data in this excel file is organized loosely in SQL-like format. The `Justifications` worksheet contains mapping from NIST controls to systems and components ids. The `Components` worksheet contains mappings from the components ids to the component names. The `References` and `Governors` worksheets contain mappings from the component ids to the information about the components and artifacts, which prove compliance.

To generate [component yamls file](https://github.com/18F/control-masonry/tree/master/data/components) execute the following command from the main directory.
```bash
python renderers/workbook_to_yamls.py
```

[**OPTION 2: writing component yamls**](https://github.com/18F/control-masonry/tree/master/data/components)
Component yamls are organizations by system and then by component. (see [Components Documentation](#components-documentation) below)

### Creating Certifications
Control Masonry generates certification documentation by pipelining the data in the [component yamls ](https://github.com/18F/control-masonry/tree/master/data/components) through the [standards yamls ](https://github.com/18F/control-masonry/tree/master/data/standards) and finally through the [certifications yamls ](https://github.com/18F/control-masonry/tree/master/data/certifications). Currently, our standards and certifications yaml documentation is a work in progress. Files are exported to the [`exports/certifications` ](https://github.com/18F/control-masonry/tree/master/exports/certifications) folder in yaml format by running:
```
python renderers/yamls_to_certification.py
```

### Creating Documentation
Control Masonry currently only supports one document format: a static site which uses [18F Guides Template](https://github.com/18F/guides-template). **Submission for new formats are welcome.**

To generate a [18F Guides Template](https://github.com/18F/guides-template) in the [export/Pages](https://github.com/18F/control-masonry/tree/master/exports/Pages) folder.
```
python renderers/certifications_to_pages.py
```

# Documentation Format

### Components Documentation
Component documentation contains information about individual system components and the standards they satisfy.
[#components-documentation]

```yaml
name: User Account and Authentication (UAA) Server
references:
- reference_name: User Account and Authentication (UAA) Server
  reference_url: http://docs.pivotal.io/pivotalcf/concepts/architecture/uaa.html
- reference_name: Creating and Managing Users with the UAA CLI (UAAC)
  reference_url: http://docs.pivotal.io/pivotalcf/adminguide/uaa-user-management.html
governors:
- governor_name: Cloud Foundry Roles
  governor_url: https://cf-p1-docs-prod.cfapps.io/pivotalcf/concepts/roles.html
- governor_name: Cloud Foundry Org Access
  governor_url: https://github.com/cloudfoundry/cloud_controller_ng/blob/master/spec/unit/access/organization_access_spec.rb
- governor_name: Cloud Foundry Space Access
  governor_url: https://github.com/cloudfoundry/cloud_controller_ng/blob/master/spec/unit/access/space_access_spec.rb
satisfies:
  AC-2: Cloud Foundry accounts are managed through the User Account and Authentication
    (UAA) Server.
  IA-2: The UAA is the identity management service for Cloud Foundry. Its primary
    role is as an OAuth2 provider, issuing tokens for client applications to use when
    they act on behalf of Cloud Foundry users.
  SC-13: All traffic from the public internet to the Cloud Controller and UAA happens
    over HTTPS and operators configure encryption of the identity store in the UAA
  SC-28 (1): Operators configure encryption of the identity store in the UAA. When
    users register an account with the Cloud Foundry platform, the UAA, acts as the
    user store and stores user passwords in the UAA database using bcrypt. Bcrypt
    is a blowfish encryption algorithm, which enables cloud foundry to store a secure
    hash of your users' passwords.
```

### Standards Documentation
Contain information about security standards.

```yaml
# nist-800-53.yaml
standards:
  C-2:
    name: User Access
    description: There is an affordance for managing access by...

# PCI.yaml
standards:
  Regulation-6:
    name: User Access PCI
    description: There is an affordance for managing access by...
```

### Certifications
Empty yaml for creating certification documentation. Serve as a template for combining controls and standards yamls.

```yaml
# Fisma.yaml
standards:
  nist-800-53:
    C-2:
    C-3:
  PCI:
    6:
```
