// Enables SDK auto-install, and uses it to run the given block
def withAndroidSdk(String sdkDir = '/tmp/android-sdk',
 Closure body) {
 // Create the SDK directory, and accept the licences
 // (see: d.android.com/r/studio-ui/export-licenses.html)
 writeFile file: "${sdkDir}/licenses/android-sdk-license",
 text: '8933bad161af4178b1185d1a37fbf41ea5269c55'
 
 writeFile file: "${sdkDir}/licenses/android-sdk-preview-license",
 text: '84831b9409646a918e30573bab4c9c91346d8abd'
 
 // Run the given closure with this SDK directory
 withEnv(["ANDROID_HOME=${sdkDir}"]) {
 body()
 }
}


node {
 stage ('Pull from SCM') {
// Check out the source code
  git 'https://github.com/sumitsaiwal/android-topeka'
 }
// Build the app using the 'debug' build type,
// and allow SDK components to auto-install
 stage ('Test and Build app') {
  withAndroidSdk {
   sh './gradlew clean assembleDebug lintDebug testDebugUnitTest'
  }
 }
 // Analyse the JUnit test results//androidLint plugin is required
 //https://github.com/jenkinsci/job-dsl-plugin/blob/master/job-dsl-core/src/main/docs/examples/javaposse/jobdsl/dsl/helpers/publisher/PublisherContext/androidLint.groovy
 stage ('Analyse the Lint results') {
  androidLint unstableTotalHigh: '0'
 }
// Analyse the JUnit test results
 stage ('Analyse JUnit test results') {
  junit '**/TEST-*.xml'
 }
// Store the APK that was built
 stage ('Archive APK') {
  archive '**/*-debug.apk'
 }
}

