## Environment Variable Loader
> This is an example of a `build.properties` file.

```properties
BUILD_ENV=TEST
VARIABLE2=true
```

> Implementation of `loadVarsFromFile.groovy`

```groovy
def call(fileName) {
    def props = readProperties file: fileName
    for (prop in props) {
        env[prop.key] = prop.value
    }
}
```

> Using the shared library method is as simple as invoking it within a script wrapper

```groovy
pipeline {
    agent any

    stages('Run Things') {
        stage('Load Files') {
            steps {
                script { loadVarsFromFile('build.properties') }
                sh 'printenv'
            }
        }
    }
}
```

For cases where you want to export the contents of file to the pipeline as environment variables, you can create a shared library function read the file and export the defined environment variables to the pipeline.

What you need to do first is create a file containing the key-value pairs. I've tried using both `.env` and `.properties` file but I'm still not sure what are the valid file extensions allowed. In this example, we create a `build.properties` file. On our sample file, we have 2 sets of key-value pairs.

### Implementation

Create the shared library function next. We name our shared library method `loadVarsFromFile.groovy`. What we do is read the file by using the built-in `readProperties`
utility step. Click <a href="https://jenkins.io/doc/pipeline/steps/pipeline-utility-steps/#readproperties-read-properties-from-files-in-the-workspace-or-text">here</a> for more pipeline utility steps. After that, we loop through the file contents and set each key-pair value as environment variable.

If you noticed, we can use the pipeline utility steps (e.g. `readProperties`, `sh`, `echo`, etc) within our shared library method because when invoking the method within the pipeline, the context of the pipeline becomes available to the method.

### Testing Testing

You can also test this function by moving the function logic to a `class` that has to implement `Serializable` and then writing another  `class` that extends the `GroovyTestCase`. Below is a sample directory structure:

<code>
jenkins-shared-library <br>
-- src <br>
&nbsp;&nbsp;&nbsp;-- com <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- journey <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- EnvironmentVariableLoader.groovy <br>
-- test <br>
&nbsp;&nbsp;&nbsp;-- com <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- journey <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- EnvironmentVariableLoaderTest.groovy <br>
</code>

Full implementation here for <a href="https://github.com/jvcudis/jenkins-journey-shared-library/blob/master/src/com/journey/EnvironmentVariableLoader.groovy" target="_blank">EnvironmentVariableLoader.groovy</a> and <a href="https://github.com/jvcudis/jenkins-journey-shared-library/blob/master/test/com/journey/EnvironmentVariableLoaderTest.groovy" target="_blank">EnvironmentVariableLoaderTest.groovy</a>.
