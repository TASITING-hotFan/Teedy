pipeline {
    agent any
    
    tools {
        // 需在 Jenkins 全局工具配置中预先配置好名为 'maven-3.x' 和 'jdk-11' 的工具
        maven 'maven-3.x'
        jdk 'jdk-11'
    }

    stages {
        stage('Maven构建 & 代码检查') {
            steps {
                echo '开始执行 Maven 清理、构建与 PMD 代码检查...'
                // pmd:pmd 会在各个子模块的 target/ 目录下生成 pmd.xml 代码检查报告
                sh 'mvn clean install pmd:pmd -DskipTests'
            }
        }

        stage('运行测试 & 生成报告') {
            steps {
                echo '开始运行单元测试并生成测试报告...'
                // 显式触发测试并允许失败时不直接中断流水线（以生成测试报告）
                sh 'mvn test -Dmaven.test.failure.ignore=true'
            }
        }

        stage('生成 JavaDoc 文档') {
            steps {
                echo '开始生成项目的 JavaDoc 归档文档...'
                // 生成 javadoc:jar 后缀归档包
                sh 'mvn javadoc:jar -DskipTests'
            }
        }
    }

    post {
        always {
            echo '=== 正在收集与归档构建输出结果 ==='
            
            // 1. 收集和展现单元测试报告（JUnit 插件自动解析 xml 并生成可视化的 html 趋势图）
            junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true
            
            // 2. 归档构建输出的：可执行文件(*.jar)、PMD测试报告(*.xml/*.html)、JavaDoc文档(*.jar)
            archiveArtifacts artifacts: '**/target/*.jar, **/target/*.war, **/target/pmd.xml', fingerprint: true, allowEmptyArchive: true
        }
    }
}
