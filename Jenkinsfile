import jenkins.model.*
import com.cloudbees.hudson.plugins.folder.*
import org.jenkinsci.plugins.workflow.job.WorkflowJob
import groovy.xml.XmlUtil
import nectar.plugins.rbac.strategy.*
import hudson.security.*
import nectar.plugins.rbac.groups.*
import nectar.plugins.rbac.roles.*

node('GROOVY') {
  stage ('Create Jenkins Folder') {
    git branch: 'feature/folder', url: 'https://globalrepository.mclocal.int/stash/scm/pipe/Jenkins-2-Folder-Registry'
    checkout scm
    folderInfoRbac()
  }
}

def folderInfoRbac() {
  RoleMatrixAuthorizationStrategyImpl strategy = RoleMatrixAuthorizationStrategyImpl.getInstance()
  RoleMatrixAuthorizationConfig config = RoleMatrixAuthorizationPlugin.getConfig()

  folderData = readYaml(file: "folders.yaml")
  folderData.each {
    String folderName = it.Name.replaceAll("\\s","")
    folderDisplayName = it.Name
    folderDescription = it.description
    def folderStatus = Jenkins.instance.getItemByFullName(folderName)
    if ( ! folderStatus ) {
      createFolder(folderName, folderDisplayName, folderDescription)
    }
    it.groups.each { gpName, gpData ->
      String groupName = gpName
      locationFolder = Jenkins.instance.getAllItems().find{it.fullName.equals(folderName)}
      GroupContainer container = GroupContainerLocator.locate(locationFolder)
      Group group = new Group(container, groupName)
      gpData.members.each {
        group.doAddMember(it)
      }
      gpData.roles.each {
        group.doGrantRole(it, 0, Boolean.TRUE)
      }
      container.addGroup(group)
    }
  }
}

def createFolder(folderName, folderDisplayName, folderDescription) {
final String FOLDER_CONFIG_XML = """<?xml version='1.0' encoding='UTF-8'?>
<com.cloudbees.hudson.plugins.folder.Folder plugin="cloudbees-folder@6.0.4">
  <actions>
    <io.jenkins.blueocean.service.embedded.BlueOceanUrlAction plugin="blueocean-rest-impl@1.1.6">
      <blueOceanUrlObject class="io.jenkins.blueocean.service.embedded.BlueOceanUrlObjectImpl">
        <mappedUrl>blue/organizations/jenkins</mappedUrl>
      </blueOceanUrlObject>
    </io.jenkins.blueocean.service.embedded.BlueOceanUrlAction>
  </actions>
  <displayName>$folderDisplayName</displayName>
  <description>$folderDescription</description>
  <properties>
    <com.cloudbees.jenkins.plugins.foldersplus.SecurityGrantsFolderProperty plugin="cloudbees-folders-plus@3.1">
      <securityGrants/>
    </com.cloudbees.jenkins.plugins.foldersplus.SecurityGrantsFolderProperty>
    <com.cloudbees.hudson.plugins.folder.properties.EnvVarsFolderProperty plugin="cloudbees-folders-plus@3.1">
      <properties></properties>
    </com.cloudbees.hudson.plugins.folder.properties.EnvVarsFolderProperty>
    <org.jenkinsci.plugins.pipeline.modeldefinition.config.FolderConfig plugin="pipeline-model-definition@1.1.6">
      <dockerLabel></dockerLabel>
      <registry plugin="docker-commons@1.8"/>
    </org.jenkinsci.plugins.pipeline.modeldefinition.config.FolderConfig>
    <com.cloudbees.hudson.plugins.folder.properties.SubItemFilterProperty plugin="cloudbees-folders-plus@3.1"/>
  </properties>
  <folderViews class="com.cloudbees.hudson.plugins.folder.views.DefaultFolderViewHolder">
    <views>
      <hudson.model.AllView>
        <owner class="com.cloudbees.hudson.plugins.folder.Folder" reference="../../../.."/>
        <name>All</name>
        <filterExecutors>false</filterExecutors>
        <filterQueue>false</filterQueue>
        <properties class="hudson.model.View\$PropertyList"/>
      </hudson.model.AllView>
    </views>
    <tabBar class="hudson.views.DefaultViewsTabBar"/>
  </folderViews>
  <healthMetrics>
    <com.cloudbees.hudson.plugins.folder.health.WorstChildHealthMetric>
      <nonRecursive>false</nonRecursive>
    </com.cloudbees.hudson.plugins.folder.health.WorstChildHealthMetric>
    <com.cloudbees.hudson.plugins.folder.health.AverageChildHealthMetric plugin="cloudbees-folders-plus@3.1"/>
    <com.cloudbees.hudson.plugins.folder.health.JobStatusHealthMetric plugin="cloudbees-folders-plus@3.1">
      <success>true</success>
      <failure>true</failure>
      <unstable>true</unstable>
      <unbuilt>true</unbuilt>
      <countVirginJobs>false</countVirginJobs>
    </com.cloudbees.hudson.plugins.folder.health.JobStatusHealthMetric>
    <com.cloudbees.hudson.plugins.folder.health.ProjectEnabledHealthMetric plugin="cloudbees-folders-plus@3.1"/>
  </healthMetrics>
  <icon class="com.cloudbees.hudson.plugins.folder.icons.StockFolderIcon"/>
</com.cloudbees.hudson.plugins.folder.Folder>
"""
Jenkins.instance.createProjectFromXML(folderName, new StringBufferInputStream(FOLDER_CONFIG_XML))
}
