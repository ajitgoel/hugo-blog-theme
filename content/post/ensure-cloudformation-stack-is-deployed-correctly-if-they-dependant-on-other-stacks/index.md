---
title: Ensure Cloudformation stack is deployed correctly if they dependant on
  other stacks.
date: 2023-01-29T18:46:23.178Z
draft: false
featured: false
tags:
  - CloudFormation
  - AWS
  - TypeScript
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
We had an issue recently where a particular CloudFormation stack(ParentStack below) was not getting deployed. This stack depended on resources in another stack(`ChildStack `below). On further investigation, we found that since the `ChildStack `was deployed first(since it was added last to the `IBaseStack `array) and it had errors deploying, the `ParentStack `would not deploy. 

`let stacks: IBaseStack[] = [];`\
`stacks.push(new ParentStack(app, 'ParentStack', deploymentProps));
const childStack= new ChildStack(app,'ChildStack', deploymentProps);
stacks.push(childStack);`

To work around this issue, we added a dependency between the ChildStack and ParentStack to ensure that the ParentStack would always get deployed first, irrespective of when the two stacks were added to the `IBaseStack` array. 

`let stacks: IBaseStack[] = [];`\
`stacks.push(new ParentStack(app, 'ParentStack', deploymentProps));
const childStack= new ChildStack(app,'ChildStack', deploymentProps);`\
`childStack.addDependency(parentStack);
stacks.push(childStack);`

Reference:` `https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib-readme.html#construct-dependencies