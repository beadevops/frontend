<template>
  <div class="animated fadeIn" v-permission="'VIEW_PORTFOLIO'">
    <!--组件-->
    <portfolio-widget-row ref="portfolioWidgetRow" />
    <div slot="footer" style="margin-bottom: 1.5rem;background: #21304C;">
      <b-row class="text-center">
        <b-col class="mb-sm-2 mb-0">
          <div class="text-muted">{{ $t('message.vulnerable_projects') }}</div>
          <div id="vulnerable_projects" style="margin: 0 auto 0;width:100px;height:100px;"></div>
          <strong>{{vulnerableProjects}} ({{vulnerableProjectPercent}}%)</strong>
        </b-col>
        <b-col class="mb-sm-2 mb-0 d-md-down-none">
          <div class="text-muted">{{ $t('message.violations_audited') }}</div>
          <div id="violations_audited" style="margin: 0 auto 0;width:100px;height:100px;"></div>
          <strong>{{auditedViolations}} ({{auditedViolationsPercent}}%)</strong>
        </b-col>
        <b-col class="mb-sm-2 mb-0">
          <div class="text-muted">{{ $t('message.vulnerable_components') }}</div>
          <div id="vulnerable_components" style="margin: 0 auto 0;width:100px;height:100px;"></div>
          <strong>{{vulnerableComponents}} ({{vulnerableComponentPercent}}%)</strong>
        </b-col>
        <b-col class="mb-sm-2 mb-0">
          <div class="text-muted">{{ $t('message.findings_audited') }}</div>
          <div id="findings_audited" style="margin: 0 auto 0;width:100px;height:100px;"></div>
          <strong>{{auditedFindings}} ({{auditedFindingPercent}}%)</strong>
        </b-col>
      </b-row>
    </div>
    <b-card>
      <b-row>
        <b-col sm="5">
          <h4 id="chart-portfolio-vulns" class="card-title mb-0">{{ $t('message.portfolio_vulnerabilities') }}</h4>
          <div class="small text-muted">
            {{$t('message.last_measurement')}}: {{lastMeasurement}}<b-link v-permission="'PORTFOLIO_MANAGEMENT'" class="font-weight-bold" style="margin-left:6px" v-on:click="refreshMetrics"><i class="fa fa-refresh"></i></b-link>
          </div>
        </b-col>
        <b-col sm="7" class="d-none d-md-block">
        </b-col>
      </b-row>
      <chart-portfolio-vulnerabilities v-if="true" ref="chartPortfolioVulnerabilities" chartId="chartPortfolioVulnerabilities" class="chart-wrapper" style="height:400px;margin-top:40px;" :height="400"></chart-portfolio-vulnerabilities>
    </b-card>
      <!--违反策略  审核进度 隐藏-->
    <b-row v-show="false">
       <b-col sm="6">
         <b-card>
           <b-row>
             <b-col sm="5">
               <h4 id="chart-violations" class="card-title mb-0">{{ $t('message.policy_violations') }}</h4>
             </b-col>
             <b-col sm="7" class="d-none d-md-block">
             </b-col>
           </b-row>
           <chart-policy-violations ref="chartPolicyViolations" chartId="chartPolicyViolations" class="chart-wrapper" style="height:200px;margin-top:40px;" :height="200"></chart-policy-violations>
         </b-card>
       </b-col>
       <b-col sm="6">
         <b-card>
           <b-row>
             <b-col sm="5">
               <h4 id="chart-auditing-progress" class="card-title mb-0">{{ $t('message.auditing_progress') }}</h4>
             </b-col>
             <b-col sm="7" class="d-none d-md-block">
             </b-col>
           </b-row>
           <chart-audited-progress ref="chartAuditedProgress" chartId="chartAuditedProgress" class="chart-wrapper" style="height:200px;margin-top:40px;" :height="200"></chart-audited-progress>
         </b-card>
       </b-col>
     </b-row>

    <b-row>
      <b-col sm="6">
        <b-card>
          <b-row>
            <b-col sm="5">
              <h4 id="chart-projects" class="card-title mb-0">{{ $t('message.projects') }}</h4>
            </b-col>
            <b-col sm="7" class="d-none d-md-block">
            </b-col>
          </b-row>
          <chart-project-vulnerabilities ref="chartProjectVulnerabilities" chartId="chartProjectVulnerabilities" class="chart-wrapper" style="height:300px;margin-top:30px;" :height="300"></chart-project-vulnerabilities>
        </b-card>
      </b-col>
      <b-col sm="6">
        <b-card>
          <b-row>
            <b-col sm="5">
              <h4 id="chart-components" class="card-title mb-0">{{ $t('message.components') }}</h4>
            </b-col>
            <b-col sm="7" class="d-none d-md-block">
            </b-col>
          </b-row>
          <chart-component-vulnerabilities ref="chartComponentVulnerabilities" chartId="chartComponentVulnerabilities" class="chart-wrapper" style="height:300px;margin-top:30px;" :height="300"></chart-component-vulnerabilities>
        </b-card>
      </b-col>
    </b-row>
    <b-row class="dashboard-bottom">
      <b-col md="12">
        <b-card v-bind:header="$t('message.portfolio_statistics')">
          <b-row>
            <b-col sm="12" lg="6">
              <b-row>
                <b-col sm="6">
                  <Callout variant="info">
                    <small class="text-muted">{{ $t('message.projects') }}</small><br>
                    <strong class="h4">{{totalProjects}}</strong>
                  </Callout>
                </b-col>
                <b-col sm="6">
                  <Callout variant="info">
                    <small class="text-muted">{{ $t('message.components') }}</small><br>
                    <strong class="h4">{{totalComponents}}</strong>
                  </Callout>
                </b-col>
              </b-row>
            </b-col>
            <b-col sm="12" lg="6">
              <b-row>
                <b-col sm="6">
                  <Callout variant="info">
                    <small class="text-muted">{{ $t('message.portfolio_vulnerabilities') }}</small><br>
                    <strong class="h4">{{vulnerabilities}}</strong>
                  </Callout>
                </b-col>
                <b-col sm="6">
                  <Callout variant="info">
                    <small class="text-muted">{{ $t('message.suppressed') }}</small><br>
                    <strong class="h4">{{suppressed}}</strong>
                  </Callout>
                </b-col>
              </b-row>
            </b-col>
          </b-row>
        </b-card>
      </b-col>
    </b-row>
  </div>
</template>

<script>
  import common from "../shared/common"
  import PortfolioWidgetRow from './dashboard/PortfolioWidgetRow'
  import ChartPortfolioVulnerabilities from './dashboard/ChartPortfolioVulnerabilities'
  import ChartProjectVulnerabilities from "./dashboard/ChartProjectVulnerabilities";
  import ChartAuditedProgress from "./dashboard/ChartAuditingProgress";
  import ChartPolicyViolations from "./dashboard/ChartPolicyViolations";
  import ChartComponentVulnerabilities from "./dashboard/ChartComponentVulnerabilities";
  import { Callout } from '@coreui/vue'
  import permissionsMixin from "../mixins/permissionsMixin";
  import $ from "jquery";

  export default {
    name: 'dashboard',
    mixins: [permissionsMixin],
    components: {
      Callout,
      PortfolioWidgetRow,
      ChartPortfolioVulnerabilities,
      ChartProjectVulnerabilities,
      ChartAuditedProgress,
      ChartPolicyViolations,
      ChartComponentVulnerabilities
    },
    data() {
      return {
        totalProjects: 0,
        vulnerableProjects: 0,
        vulnerableProjectPercent: 0,

        totalComponents: 0,
        vulnerableComponents: 0,
        vulnerableComponentPercent: 0,

        totalFindings: 0,
        auditedFindings: 0,
        auditedFindingPercent: 0,

        totalViolations: 0,
        auditedViolations: 0,
        auditedViolationsPercent: 0,

        vulnerabilities: 0,
        suppressed: 0,
        lastMeasurement: ""
      }
    },
    methods: {
      extractStats(metrics) {
        if (!metrics || metrics.length === 0) {
          return;
        }
        let metric = metrics[metrics.length - 1]; //Use the most recent metric
        this.totalProjects = common.valueWithDefault(metric.projects, "0");
        this.vulnerableProjects = common.valueWithDefault(metric.vulnerableProjects, "0");
        this.vulnerableProjectPercent = common.calcProgressPercent(this.totalProjects, this.vulnerableProjects);
        // 动态仪表盘
        $("#vulnerable_projects").html('');
        $("#vulnerable_projects").lu_word(this.$t('message.vulnerable_projects'),2);
        $("#vulnerable_projects").setWord(this.vulnerableProjectPercent/100, this.vulnerableProjectPercent/100);

        this.totalComponents = common.valueWithDefault(metric.components, "0");
        this.vulnerableComponents = common.valueWithDefault(metric.vulnerableComponents, "0");
        this.vulnerableComponentPercent = common.calcProgressPercent(this.totalComponents, this.vulnerableComponents);
        // 动态仪表盘
        $("#vulnerable_components").html('');
        $("#vulnerable_components").lu_word(this.$t('message.vulnerable_components'),3);
        $("#vulnerable_components").setWord(this.vulnerableComponentPercent/100, this.vulnerableComponentPercent/100);

        this.totalComponents = common.valueWithDefault(metric.components, "0");
        this.totalFindings = common.valueWithDefault(metric.findingsTotal, "0");
        this.auditedFindings = common.valueWithDefault(metric.findingsAudited, "0");
        this.auditedFindingPercent = common.calcProgressPercent(this.findingsTotal, this.findingsAudited);
        // 动态仪表盘
        $("#findings_audited").html('');
        $("#findings_audited").lu_word(this.$t('message.findings_audited'),4);
        $("#findings_audited").setWord(this.auditedFindingPercent/100, this.auditedFindingPercent/100);

        this.totalViolations = common.valueWithDefault(metric.policyViolationsTotal, "0");
        this.auditedViolations = common.valueWithDefault(metric.policyViolationsAudited, "0");
        this.auditedViolationsPercent = common.calcProgressPercent(this.policyViolationsTotal, this.policyViolationsAudited);
        // 动态仪表盘
        $("#violations_audited").html('');
        $("#violations_audited").lu_word(this.$t('message.violations_audited'),5);
        $("#violations_audited").setWord(this.auditedViolationsPercent/100, this.auditedViolationsPercent/100);

        this.vulnerabilities = common.valueWithDefault(metric.vulnerabilities, "0");
        this.suppressed = common.valueWithDefault(metric.suppressed, "0");
        this.lastMeasurement = common.formatTimestamp(metric.lastOccurrence, true);
      },
      refreshMetrics() {
        let url = `${this.$api.BASE_URL}/${this.$api.URL_METRICS}/portfolio/refresh`;
        this.axios.get(url).then((response) => {
          this.$toastr.s(this.$t('message.metric_refresh_requested'));
        });
      }
    },
    mounted () {
      if (this.isPermitted(this.PERMISSIONS.VIEW_PORTFOLIO)) {
        const daysBack = 90;
        let url = `${this.$api.BASE_URL}/${this.$api.URL_METRICS}/portfolio/${daysBack}/days`;
        this.axios.get(url).then((response) => {
          this.$refs.portfolioWidgetRow.render(response.data)
          this.$refs.chartPortfolioVulnerabilities.render(response.data);
          this.$refs.chartProjectVulnerabilities.render(response.data);
          this.$refs.chartAuditedProgress.render(response.data);
          this.$refs.chartPolicyViolations.render(response.data);
          this.$refs.chartComponentVulnerabilities.render(response.data);
          this.extractStats(response.data);
        });
      }
    }
  }
</script>

<style>
  /* IE fix */
  #card-chart-01, #card-chart-02 {
    width: 100% !important;
  }
  .animated-title {
    margin-bottom: 1.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    /*background-color: hotpink;*/
  }
  .dashboard-bottom .callout {
   border-left: 20px solid #ff8f00 !important;
  }
</style>
