<template>
  <div class="top-nav">
    <div class="item">
      <dv-border-box-12>
        <div class="item-left">
          <img src="../../../public/images/AccumulatedProject.svg" alt="">
        </div>
        <div class="item-right">
          <p>{{ $t('message.portfolio_vulnerabilities') }}</p>
          <h4 class="mb-0">{{ portfolioVulnerabilities }}</h4>
        </div>
      </dv-border-box-12>
    </div>
    <div class="item">
      <dv-border-box-12>
        <div class="item-left">
          <img src="../../../public/images/Risk.svg" alt="">
        </div>
        <div class="item-right">
          <p>{{ $t('message.projects_at_risk') }}</p>
          <h4 class="mb-0">{{ vulnerableProjects }}</h4>
        </div>
      </dv-border-box-12>
    </div>
    <div class="item">
      <dv-border-box-12>
        <div class="item-left">
          <img src="../../../public/images/Vulnerable.svg" alt="">
        </div>
        <div class="item-right">
          <p>{{ $t('message.vulnerable_components') }}</p>
          <h4 class="mb-0">{{ vulnerableComponents }}</h4>
        </div>
      </dv-border-box-12>
    </div>
    <div class="item">
      <dv-border-box-12>
        <div class="item-left">
          <img src="../../../public/images/AccumulatedRisk.svg" alt="">
        </div>
        <div class="item-right">
          <p>{{ $t('message.inherited_risk_score') }}</p>
          <h4 class="mb-0">{{ inheritedRiskScore }}</h4>
        </div>
      </dv-border-box-12>
    </div>
</div>
</template>

<script>
  import WidgetPortfolioVulnerabilities from "./WidgetPortfolioVulnerabilities";
  import WidgetProjectsAtRisk from "./WidgetProjectsAtRisk";
  import WidgetVulnerableComponents from "./WidgetVulnerableComponents";
  import WidgetInheritedRiskScore from "./WidgetInheritedRiskScore";

  export default {
    components: {
      WidgetPortfolioVulnerabilities,
      WidgetProjectsAtRisk,
      WidgetVulnerableComponents,
      WidgetInheritedRiskScore,
    },
    props: {
      fetch: {
        default: false,
        type: Boolean
      }
    },
    data() {
      return {
        portfolioVulnerabilities: 0,
        vulnerableProjects: 0,
        vulnerableComponents: 0,
        inheritedRiskScore: 0
      }
    },
    beforeMount () {
      if (this.fetch) {
        const daysBack = 90;
        let url = `${this.$api.BASE_URL}/${this.$api.URL_METRICS}/portfolio/${daysBack}/days`;
        this.axios.get(url).then((response) => {
          this.render(response.data);
        });
      }
    },
    methods: {
      render: function (metrics) {
        this.portfolioVulnerabilities = metrics[metrics.length-1].vulnerabilities;
        this.vulnerableProjects = metrics[metrics.length-1].vulnerableProjects;
        this.vulnerableComponents = metrics[metrics.length-1].vulnerableComponents;
        this.inheritedRiskScore = metrics[metrics.length-1].inheritedRiskScore;
      }
    }
  }
</script>

<style>
  .top-nav {
    background-image: -ms-linear-gradient();
    display: flex;
    align-items: center;
    justify-content: space-around;
    width: 100%;
    height: 100%;
    margin-bottom: 1.5rem;
  }
  .top-nav .item {
    width: 27rem;
    height: 10rem;
    background-color: #21304C;
  }
  .top-nav .item .border-box-content {
    padding: 1rem;
    display: flex;
    align-items: center;
  }
  .top-nav .item .item-left {
    width: 13rem;
    height: 80%;
    margin-right: 2rem;
  }
  .top-nav .item .item-left img {
    display: block;
    width: 100%;
    height: 100%;
  }
  .top-nav .item .dv-border-box-12 .dv-border-svg-container {
    display: none;
  }
</style>
