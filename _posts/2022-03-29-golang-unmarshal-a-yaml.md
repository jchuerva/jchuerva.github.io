---
title: Go - Unmarshal a yaml file
date: 2022-03-29 07:25:00 +01:00
tags: [golang, yaml]
description: Go - Unmarshal a yaml file
---

We can use the [`"gopkg.in/yaml.v3"`](https://gopkg.in/yaml.v3) package for it.

First, we need to define the `struct` we want to unmarshal. For example, if we have this `data.yaml` file:

```yaml
---
sha: abcd1234
service_catalog:
  version: 1
  ownership:
  - name: service1
    long_name: service1
    description: service1 description.
    kind: logical
    maintainer: user1
    team: team1
    exec_sponsor: user1
    product_manager: user2
    repo: https://github.com/repo/repo1
    team_slack: channel1
    sev2:
      issue: https://github.com/repo/repo1/issues
      tta: 1 business day
    sev3:
      slack: channel1
    qos: critical
    tier: 1
  - name: github/service2
    long_name: service2
    description: service2 description.
    kind: logical
    maintainer: user3
    team: team2
    exec_sponsor: user4
    product_manager: user4
    repo: https://github.com/github/repo2
    team_slack: channel2
    sev2:
      issue: https://github.com/github/repo2/issues
      tta: 1 business day
    sev3:
      slack: channel2
    qos: best_effort
    dependencies:
    - github
    tier: 2
files:
  "file1":
    codeowners:
    - "@github/team2"
    maintainer_team: "@github/team2"
    reviewer_teams:
    - "@github/team2"
    service: github/service2
  "file2":
    service: github/service2
```

We need to define the following structs:

```golang
type ImportData struct {
	Sha            string          `yaml:"sha"`
	ServiceCatalog ServiceCatalog  `yaml:"service_catalog"`
	FilesData      map[string]File `yaml:"files"`
}

type File struct {
	ServiceName string `yaml:"service"`
}

type ServiceCatalog struct {
	Version  int       `yaml:"version"`
	Services []Service `yaml:"ownership"`
}

type Service struct {
	Name           string `yaml:"name"`
	LongName       string `yaml:"long_name"`
	Description    string `yaml:"description"`
	Kind           string `yaml:"kind"`
	Maintainer     string `yaml:"maintainer"`
	Team           string `yaml:"team"`
	ExecSponsor    string `yaml:"exec_sponsor"`
	ProductManager string `yaml:"product_manager"`
	Repo           string `yaml:"repo"`
	TeamSlack      string `yaml:"team_slack"`
	QOS            string `yaml:"qos"`
	Tier           string `yaml:"tier"`
}
```

Then, we can unmarshal the `data.yaml` file:

```golang
yfile, err := ioutil.ReadFile("data.yaml")
	if err != nil {
		log.Fatal(err)
	}

	data_imported := ImportData{}
  // Let's unmarshal the yaml file in data_imported
	err2 := yaml.Unmarshal(yfile, &data_imported)
	if err2 != nil {
		log.Fatal(err2)
	}

  // we can now use data_imported and access to the data
	fmt.Println("Sha:", data_imported.Sha) // abcd1234
	fmt.Println("----Service Catalog----")
	fmt.Println("Version:", data_imported.ServiceCatalog.Version) // 1

  for _, service := range data_imported.ServiceCatalog.Services {
    fmt.Println("Name:", service.Name)
    // service1
    // service2
  }
```