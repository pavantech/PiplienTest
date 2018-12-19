import com.cloudbees.opscenter.server.clusterops.steps.MasterGroovyClusterOpStep
import com.cloudbees.opscenter.server.model.ConnectedMaster
import hudson.model.StreamBuildListener
import jenkins.model.Jenkins
import jenkins.model.*
import com.cloudbees.hudson.plugins.folder.*
import org.jenkinsci.plugins.workflow.job.WorkflowJob
import groovy.xml.XmlUtil
import nectar.plugins.rbac.strategy.*
import hudson.security.*
import nectar.plugins.rbac.groups.*
import nectar.plugins.rbac.roles.*
import hudson.FilePath
import hudson.FilePath.FileCallable;
node('master'){
folderData = readYaml(file: "folders.yaml")
  folderData.each {
    String folderName = it.Name.replaceAll("\\s","")
    folderDisplayName = it.Name
    folderDescription = it.description
    println(folderDisplayName)
    println(folderDisplayName)
    println(folderDisplayName)
  }
}
