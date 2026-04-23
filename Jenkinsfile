import java.util.regex.Pattern

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/joan-domingo/jenkins-test.git'
            }
        }

        stage('Create Semantic Version Tag (master only)') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'joan-domingo', usernameVariable: 'GITHUB_APP', passwordVariable: 'GITHUB_ACCESS_TOKEN')]) {
                        sh 'git config --global user.name joan-domingo'
                        sh 'git config --global user.email joands@gmail.com'

                        sh 'git fetch --tags'

                        def lastTag = sh(
                            script: "git tag -l --sort=-v:refname 'v[0-9]*' | head -1",
                            returnStdout: true
                        ).trim()

                        def commits = sh(
                            script: "git log ${lastTag}..HEAD --no-merges --pretty=format:'%s%n%b'",
                            returnStdout: true
                        ).trim()

                        def newTag = bumpSemanticVersion(lastTag, commits)

                        echo "Creating new semantic version tag: ${newTag} (previous: ${lastTag ?: 'none'})"
                        sh "git tag -a \"${newTag}\" -m 'Release ${newTag}'"
                        sh "git push https://x-access-token:${GITHUB_ACCESS_TOKEN}@github.com/joan-domingo/jenkins-test.git \"${newTag}\""

                        env.PREVIOUS_VERSION_TAG = lastTag
                        env.NEW_VERSION_TAG = newTag
                    }
                }
            }
        }
    }
}

// @NonCPS
def bumpSemanticVersion(String lastTag, String commits) {
    if (!lastTag) {
        return 'v1.0.0'
    }
    def versionMatcher = (lastTag =~ /^v?(\d+)\.(\d+)\.(\d+)/)
    if (!versionMatcher.find()) {
        return 'v1.0.0'
    }
    def major = versionMatcher[0][1].toInteger()
    def minor = versionMatcher[0][2].toInteger()
    def patch = versionMatcher[0][3].toInteger()

    def breakingPattern = Pattern.compile("(?m)^[a-z]+(\\(.+\\))?!:")
    def featPattern = Pattern.compile("(?m)^feat(\\(.+\\))?:")
    if (commits.contains('BREAKING CHANGE') || breakingPattern.matcher(commits).find()) {
        return "v${major + 1}.0.0"
    } else if (featPattern.matcher(commits).find()) {
        return "v${major}.${minor + 1}.0"
    } else {
        return "v${major}.${minor}.${patch + 1}"
    }
}