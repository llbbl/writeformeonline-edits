# Structuring Terraform Projects Using Terragrunt — Part I

I've noticed a reasonable gap regarding the wisdom on how to configure terraform projects. So I decided to write a two-part series on configuring your terraform projects. 

We will cover topics ranging from small and medium-sized projects to significantly large-scale projects. Finally, I'll discuss the pros and cons of using terragrunt and some tips on getting it working for your projects.

I recently read a very excellent write-up by the founder of Gruntwork, Yevgeniy Brikman, in which he compares using terraform workspaces, multiple branches, and terragrunt to configure different environments. 

It is a 4 part series, and even though it might seem like a biased perspective from the founder himself, the author was entirely on point with many comparative aspects that he mentioned in his posts.

## [How to manage multiple environments with Terraform](https://blog.gruntwork.io/how-to-manage-multiple-environments-with-terraform-32c7bc5d692)
I would highly encourage you to take a moment to go through those series and understand why relying on different branching structures or using variables using the `.tfvars` file is not enough. 

## [Terraspace | The Terraform Framework](https://terraspace.cloud/)

Yevgeniy, however, didn't cater to terraspace in his write-up, which is a direct competitor of terragrunt, with more opinions on how to configure your terraform project. 

## [Pulumi - Universal Infrastructure as Code](https://www.pulumi.com/)

Another emerging tool in this space is Pulumi which removes the need to learn the `.hcl` language and brings the infrastructure and cloud configuration to every developer. In addition, Pulumi allows its users to code in the language of their choice to generate infrastructure for multiple cloud environments.

  
## [Creating an ECS Fargate service for container applications using terraform and terragrunt](https://itnext.io/creating-an-ecs-fargate-service-for-container-applications-using-terraform-and-terragrunt-2af5db3b35c0)

The structuring process is relatively straightforward for small to medium-sized terraform projects. I would take the example of a recent post that I published, creating an AWS ECS Fargate cluster for containerized applications on AWS using terraform and terragrunt.

In my experience, Terraform projects must be structured correctly from the beginning. It gets relatively harder to adopt drastic changes as the project develops, as you cannot make subtle changes without messing up a significant chunk of infrastructure. 

It is also essential to consider the different environments you'd be creating in your cloud so that the terragrunt configuration can reflect that well. A subtle point to note here is that this needs to be well thought through since the beginning so that you can seamlessly progress as the project matures.

The first ingredient to structuring your infrastructure is understanding how all the structures are connected. 

You might have the following components in a clean ECS project. 

1.  [AWS VPC](https://aws.amazon.com/vpc/)
2.  [AWS ECS cluster](https://aws.amazon.com/ecs/) 
3.  [AWS Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/)

I created terraform code for those components and started configuring the terragrunt configuration in a way I would want my infrastructure to layer on top of each other. 

The base layer components, in this case, `VPC,` `ECS` cluster, and `ALB,` were part of `base_infrastructure,` which was to be set up in the beginning, and the containerized application would then be layered on top of the `base_infrastructure.` 

Extra care should be taken when identifying and configuring components in the `base_infrastructure`; the base layer has to be stable and well-configured right from the beginning. 

This structure is so that we don't introduce many changes later, which can lead to the recreation of these components, which, most of the time, would not be possible if layered components were dependent on them.

Once the requirements have been broken down into `base_infrastructure` and `applications` components, you can start writing some code. Start with creating two base folders; `terraform` and `terragrunt.` 

All `.tf` code goes in the `terraform` folder, whereas all `.hcl` configuration goes in the `terragrunt` folder. Modules in the `terraform` folder can have a flat hierarchy, where all the components exist simultaneously, ideally in their folders. 

The `terragrunt` configuration exists in the hierarchy in the `terragrunt` folder, which is further split into `base_infrastructure` and `applications` sub-hierarchy. Another thing to cater to would be the different environments you'd be creating your infrastructure for, namely `development,` `staging,` or `production` environments. 

You'd need to introduce further sub-hierarchy within the folders under `terragrunt` to cater for `dev,` `staging,` and `prod` environments. Then, create the modules that belong to that infrastructure hierarchy once those environment folders are in place.

There is no official guideline on this, but over the past years, with multiple infrastructure projects, I've realized with experience that this is quite a logical way to distribute your infrastructure components so that there is less impact of changes on other components in the architecture. 

This setup has pros and cons; this would be ideal in case your project is not very extensive, and it doesn't take 15 minutes to go through the entire folder structure to find the `terragrunt` file you need to configure or edit. 

The separation of configuration for modules allows you to define [dependencies](https://terragrunt.gruntwork.io/docs/reference/config-blocks-and-attributes/#dependency) so that the base modules are configured before the dependent modules. 

The project structure is extensibility friendly; if you need to add a new module to your infrastructure, you just need to create a new `terraform` module and add the `terragrunt` configuration in the respective folder.

## [Configuration Blocks and Attributes](https://terragrunt.gruntwork.io/docs/reference/config-blocks-and-attributes)

It might seem a lot, but basically, it's relatively straightforward.

Let me know if you have any comments, suggestions, or improvements regarding this setup. Also, if you're using an alternate design for your terraform projects, please let me know in the comments. Finally, it would be interesting to hear how others are tackling this.