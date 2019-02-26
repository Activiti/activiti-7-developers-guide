# Contributing

If you want to contribute get in touch with the project members and ask for guidance. This project is a community driven project, so get in touch if you want to participate. The best place to contact the team for contributions is [gitter](https://gitter.im/Activiti/Activiti7)

## Pull Requests Spanning Repositories

Activiti now uses fixed versions instead of maven snapshot versions. This changes how to create pull requests that span multiple repositories.

For example, say you are working on related changes in both the Activiti/Acitiviti repository and also `activiti-cloud-runtime-bundle-service`. The changes in `activiti-cloud-runtime-bundle-service` will depend on the changes here. To use these changes you need to build the artifacts from your modified version of the Activiti/Activiti repo against the modified version of `activiti-cloud-runtime-bundle-service`.

So in activiti-cloud-runtime-bundle-service repository we need to apply the SNAPSHOT version. We can do this by taking the name of the maven artifact ending with `-dependencies` from the repo and applying this as a new dependency entry in the `dependencyManagement` section of module ending with `-dependencies` in the target `activiti-cloud-runtime-bundle-service` repository. This means we locally add the entry:

```markup
      <dependency>
        <groupId>org.activiti</groupId>
        <artifactId>activiti-core-dependencies</artifactId>
        <version>7.0.0-SNAPSHOT</version>
        <scope>import</scope>
        <type>pom</type>
      </dependency>
```

as the first entry in the `dependencyManagement` section of the `activiti-cloud-runtime-bundle-dependencies` module of `activiti-cloud-runtime-bundle-service`.

The entry needs to be first so that it takes priority over other pom imports \(boms\). The same process should for applying other changes to downstream repositories. For more on the sequence of repositories see [Repositories](repositories.md) or [this blog post on CI and CD](https://community.alfresco.com/community/bpm/blog/2018/11/05/activiti-cloud-cicd-approach-for-java-libraries-and-beyond)

