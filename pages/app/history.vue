<script setup lang="ts">
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  BarElement,
  ArcElement,
} from 'chart.js'
import { Bar, Doughnut } from 'vue-chartjs'
import { ref, computed, onMounted } from 'vue'

// Register ChartJS components
ChartJS.register(
  Title,
  Tooltip,
  Legend,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  BarElement,
  ArcElement
)

// Mock data for development - replace with actual API call
const apiData = ref({
  "time_range": {
    "start": "2025-05-31T07:47:53.230000",
    "end": "2025-05-31T08:21:18.009000"
  },
  "total_messages_analyzed": 14,
  "compliance_scores": {
    "average_score": 0.2285714285714286,
    "score_distribution": {
      "0.0-0.2": 11,
      "0.2-0.4": 2,
      "0.4-0.6": 1,
      "0.6-0.8": 0,
      "0.8-1.0": 0
    },
    "trend": [
      {
        "date": "2025-05-31T00:00:00",
        "score": 0.2285714285714286
      }
    ]
  },
  "classifications": {
    "distribution": {
      "Review Needed": 1,
      "Non-compliant": 13,
      "Compliant": 0
    },
    "by_region": {
      "EASTERN": {
        "Review Needed": 1,
        "Non-compliant": 13,
        "Compliant": 0
      }
    },
    "by_industry": {
      "HEALTHCARE": {
        "Non-compliant": 13,
        "Compliant": 0
      },
      "TECHNOLOGY": {
        "Review Needed": 1
      }
    }
  },
  "violations": {
    "top_categories": [
      {
        "category": "Missing Opt-Out Mechanism",
        "count": 26
      },
      {
        "category": "Missing Opt-out Mechanism",
        "count": 8
      },
      {
        "category": "Opt-out Omission",
        "count": 7
      },
      {
        "category": "Missing Opt-Out",
        "count": 5
      },
      {
        "category": "Loan Spam/Opt-Out Clarity",
        "count": 5
      }
    ],
    "frequent_violations": [
      {
        "violation": "Potentially misleading or clickbait language",
        "count": 2
      },
      {
        "violation": "No opt-out instructions included",
        "count": 2
      },
      {
        "violation": "No evidence of consumer consent or opt-in",
        "count": 1
      },
      {
        "violation": "Potentially misleading/overly promotional language",
        "count": 1
      },
      {
        "violation": "Potential violation of TCPA and CAN-SPAM Act requirements",
        "count": 1
      }
    ],
    "by_region": {/* ... your region data ... */},
    "by_industry": {/* ... your industry data ... */}
  },
  "regional_metrics": {
    "compliance_rates": {
      "EASTERN": 0.0
    }
  },
  "industry_metrics": {
    "compliance_rates": {
      "HEALTHCARE": 0.65,
      "TECHNOLOGY": 0.78,
      "FINANCE": 0.82,
      "RETAIL": 0.71
    }
  },
  "temporal_metrics": {
    "daily_trend": [
      {
        "date": "2025-05-31T00:00:00",
        "compliance_rate": 0.0,
        "average_score": 0.2285714285714286
      }
    ]
  }
})

const loading = ref(false)

// Chart Options
const baseChartOptions = {
  responsive: true,
  maintainAspectRatio: true,
  plugins: {
    legend: {
      position: 'top' as const,
      labels: {
        padding: 8,
        boxWidth: 8,
        font: { size: 10 }
      }
    },
    tooltip: {
      backgroundColor: 'rgba(255, 255, 255, 0.95)',
      titleColor: '#334155',
      bodyColor: '#334155',
      borderColor: '#e2e8f0',
      borderWidth: 1,
      padding: 8,
      boxPadding: 4,
      usePointStyle: true,
      bodyFont: { size: 11 },
      titleFont: { size: 11 }
    }
  }
}

// Computed Chart Data
const complianceScoreChart = computed(() => ({
  labels: Object.keys(apiData.value.compliance_scores.score_distribution),
  datasets: [{
    label: 'Messages',
    data: Object.values(apiData.value.compliance_scores.score_distribution),
    backgroundColor: [
      'rgba(239, 68, 68, 0.7)',  // red for low scores
      'rgba(249, 115, 22, 0.7)', // orange
      'rgba(234, 179, 8, 0.7)',  // yellow
      'rgba(34, 197, 94, 0.7)',  // light green
      'rgba(21, 128, 61, 0.7)'   // dark green for high scores
    ],
    borderColor: 'rgba(255, 255, 255, 0.8)',
    borderWidth: 2
  }]
}))

const classificationsChart = computed(() => ({
  labels: Object.keys(apiData.value.classifications.distribution),
  datasets: [{
    data: Object.values(apiData.value.classifications.distribution),
    backgroundColor: [
      'rgba(234, 179, 8, 0.7)',  // yellow for review
      'rgba(239, 68, 68, 0.7)',  // red for non-compliant
      'rgba(34, 197, 94, 0.7)'   // green for compliant
    ],
    borderColor: 'rgba(255, 255, 255, 0.8)',
    borderWidth: 2
  }]
}))

const topViolationsChart = computed(() => ({
  labels: apiData.value.violations.top_categories.map(v => v.category),
  datasets: [{
    label: 'Occurrences',
    data: apiData.value.violations.top_categories.map(v => v.count),
    backgroundColor: 'rgba(239, 68, 68, 0.7)',
    borderColor: 'rgba(239, 68, 68, 1)',
    borderWidth: 1
  }]
}))

const industryComplianceChart = computed(() => ({
  labels: Object.keys(apiData.value.industry_metrics.compliance_rates),
  datasets: [{
    label: 'Compliance Rate',
    data: Object.values(apiData.value.industry_metrics.compliance_rates).map(rate => (rate * 100).toFixed(1)),
    backgroundColor: Object.values(apiData.value.industry_metrics.compliance_rates).map(rate => 
      rate >= 0.8 ? 'rgba(21, 128, 61, 0.7)' :  // Dark green for high compliance
      rate >= 0.7 ? 'rgba(34, 197, 94, 0.7)' :  // Light green for good compliance
      rate >= 0.6 ? 'rgba(234, 179, 8, 0.7)' :  // Yellow for moderate compliance
                    'rgba(239, 68, 68, 0.7)'     // Red for low compliance
    ),
    borderColor: 'rgba(255, 255, 255, 0.8)',
    borderWidth: 1
  }]
}))

// Risk level calculation helper
const calculateRiskLevel = (data: any) => {
  // Factors to consider for risk assessment
  const factors = {
    // Average compliance score weight (40%)
    complianceScore: {
      value: data.compliance_scores.average_score,
      weight: 0.4,
      score: () => {
        const score = data.compliance_scores.average_score;
        return score >= 0.8 ? 1 : score >= 0.6 ? 0.5 : 0;
      }
    },
    
    // Non-compliant messages ratio weight (30%)
    nonCompliantRatio: {
      value: data.classifications.distribution["Non-compliant"] / data.total_messages_analyzed,
      weight: 0.3,
      score: () => {
        const ratio = data.classifications.distribution["Non-compliant"] / data.total_messages_analyzed;
        return ratio <= 0.2 ? 1 : ratio <= 0.4 ? 0.5 : 0;
      }
    },
    
    // Violation severity weight (30%)
    violationSeverity: {
      value: data.violations.top_categories.reduce((acc: number, curr: any) => acc + curr.count, 0) / data.total_messages_analyzed,
      weight: 0.3,
      score: () => {
        const severityRatio = data.violations.top_categories.reduce((acc: number, curr: any) => acc + curr.count, 0) / data.total_messages_analyzed;
        return severityRatio <= 1 ? 1 : severityRatio <= 2 ? 0.5 : 0;
      }
    }
  };

  // Calculate weighted risk score
  const riskScore = Object.values(factors).reduce((total, factor) => {
    return total + (factor.score() * factor.weight);
  }, 0);

  // Determine risk level and color
  if (riskScore >= 0.8) {
    return {
      level: '1',
      color: 'text-gray-700',
      score: riskScore,
      details: {
        complianceScore: factors.complianceScore.value,
        nonCompliantRatio: factors.nonCompliantRatio.value,
        violationsPerMessage: factors.violationSeverity.value
      }
    };
  } else if (riskScore >= 0.5) {
    return {
      level: '2',
      color: 'text-gray-700',
      score: riskScore,
      details: {
        complianceScore: factors.complianceScore.value,
        nonCompliantRatio: factors.nonCompliantRatio.value,
        violationsPerMessage: factors.violationSeverity.value
      }
    };
  } else {
    return {
      level: '3',
      color: 'text-gray-700',
      score: riskScore,
      details: {
        complianceScore: factors.complianceScore.value,
        nonCompliantRatio: factors.nonCompliantRatio.value,
        violationsPerMessage: factors.violationSeverity.value
      }
    };
  }
};

// Computed properties
const criticalMetrics = computed(() => ({
  risk: calculateRiskLevel(apiData.value),
  totalViolations: apiData.value.violations.top_categories.reduce((acc, curr) => acc + curr.count, 0)
}))

onMounted(() => {
  // In production, replace this with actual API call
  loading.value = false
})
</script>

<template>
  <div class="min-h-screen bg-gray-50 p-4">
    <div v-if="loading" class="flex items-center justify-center h-screen">
      <div class="animate-spin rounded-full h-32 w-32 border-b-2 border-blue-500"></div>
    </div>

    <div v-else class="max-w-7xl mx-auto space-y-4">
      <!-- Header -->
      <div class="bg-white rounded-xl shadow-sm p-4">
        <h1 class="text-xl font-bold text-gray-900">Compliance Analytics Dashboard</h1>
        <p class="text-sm text-gray-600">
          {{ new Date(apiData.time_range.start).toLocaleString() }} - 
          {{ new Date(apiData.time_range.end).toLocaleString() }}
        </p>
      </div>

      <!-- Critical Metrics -->
      <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
        <div class="bg-white rounded-xl shadow-sm p-3">
          <div class="flex items-center justify-between">
            <h3 class="text-xs font-medium text-gray-500">Risk Level</h3>
            <span class="text-lg font-bold text-gray-700">{{ criticalMetrics.risk.level }}</span>
          </div>
          <div class="mt-2 space-y-1">
            <div class="flex items-center justify-between text-xs">
              <span class="text-gray-500">Compliance Score:</span>
              <span :class="criticalMetrics.risk.details.complianceScore >= 0.8 ? 'text-green-600' : 
                            criticalMetrics.risk.details.complianceScore >= 0.6 ? 'text-yellow-600' : 'text-red-600'">
                {{ (criticalMetrics.risk.details.complianceScore * 100).toFixed(1) }}%
              </span>
            </div>
            <div class="flex items-center justify-between text-xs">
              <span class="text-gray-500">Non-compliant Ratio:</span>
              <span :class="criticalMetrics.risk.details.nonCompliantRatio <= 0.2 ? 'text-green-600' : 
                            criticalMetrics.risk.details.nonCompliantRatio <= 0.4 ? 'text-yellow-600' : 'text-red-600'">
                {{ (criticalMetrics.risk.details.nonCompliantRatio * 100).toFixed(1) }}%
              </span>
            </div>
            <div class="flex items-center justify-between text-xs">
              <span class="text-gray-500">Violations/Message:</span>
              <span :class="criticalMetrics.risk.details.violationsPerMessage <= 1 ? 'text-green-600' : 
                            criticalMetrics.risk.details.violationsPerMessage <= 2 ? 'text-yellow-600' : 'text-red-600'">
                {{ criticalMetrics.risk.details.violationsPerMessage.toFixed(1) }}
              </span>
            </div>
          </div>
        </div>
        <div class="bg-white rounded-xl shadow-sm p-3">
          <h3 class="text-xs font-medium text-gray-500">Average Score</h3>
          <p class="text-lg font-bold" 
             :class="apiData.compliance_scores.average_score < 0.6 ? 'text-red-600' : 
                    apiData.compliance_scores.average_score < 0.8 ? 'text-yellow-600' : 'text-green-600'">
            {{ (apiData.compliance_scores.average_score * 100).toFixed(1) }}%
          </p>
        </div>
        <div class="bg-white rounded-xl shadow-sm p-3">
          <h3 class="text-xs font-medium text-gray-500">Messages Analyzed</h3>
          <p class="text-lg font-bold text-blue-600">{{ apiData.total_messages_analyzed }}</p>
        </div>
        <div class="bg-white rounded-xl shadow-sm p-3">
          <h3 class="text-xs font-medium text-gray-500">Total Violations</h3>
          <p class="text-lg font-bold text-red-600">{{ criticalMetrics.totalViolations }}</p>
        </div>
      </div>

      <!-- Charts Grid -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
        <!-- Score Distribution -->
        <div class="bg-white rounded-xl shadow-sm p-4">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Compliance Score Distribution</h3>
          <div class="h-[180px]">
            <Bar :data="complianceScoreChart" 
                 :options="{ 
                   ...baseChartOptions,
                   aspectRatio: 1.8,
                   plugins: {
                     ...baseChartOptions.plugins,
                     legend: {
                       ...baseChartOptions.plugins.legend,
                       display: false
                     }
                   },
                   scales: {
                     y: {
                       beginAtZero: true,
                       ticks: {
                         font: { size: 10 }
                       }
                     },
                     x: {
                       ticks: {
                         font: { size: 10 }
                       }
                     }
                   }
                 }" />
          </div>
        </div>

        <!-- Classifications -->
        <div class="bg-white rounded-xl shadow-sm p-4">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Message Classifications</h3>
          <div class="h-[180px]">
            <Doughnut :data="classificationsChart" 
                      :options="{ 
                        ...baseChartOptions,
                        aspectRatio: 1.8,
                        plugins: {
                          ...baseChartOptions.plugins,
                          legend: {
                            ...baseChartOptions.plugins.legend,
                            position: 'right',
                            labels: {
                              ...baseChartOptions.plugins.legend.labels,
                              font: { size: 10 }
                            }
                          }
                        }
                      }" />
          </div>
        </div>

        <!-- Top Violations -->
        <div class="bg-white rounded-xl shadow-sm p-4">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Top Violation Categories</h3>
          <div class="h-[180px]">
            <Bar :data="topViolationsChart" 
                 :options="{ 
                   ...baseChartOptions,
                   aspectRatio: 1.8,
                   plugins: {
                     ...baseChartOptions.plugins,
                     legend: {
                       display: false
                     }
                   },
                   scales: {
                     y: {
                       beginAtZero: true,
                       ticks: {
                         font: { size: 10 }
                       }
                     },
                     x: {
                       ticks: {
                         font: { size: 10 }
                       }
                     }
                   }
                 }" />
          </div>
        </div>

        <!-- Industry Compliance -->
        <div class="bg-white rounded-xl shadow-sm p-4">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Industry Compliance Rates</h3>
          <div class="h-[180px]">
            <Bar :data="industryComplianceChart" 
                 :options="{ 
                   ...baseChartOptions,
                   aspectRatio: 1.8,
                   plugins: {
                     ...baseChartOptions.plugins,
                     legend: {
                       display: false
                     }
                   },
                   scales: {
                     y: {
                       beginAtZero: true,
                       ticks: {
                         font: { size: 10 }
                       }
                     },
                     x: {
                       ticks: {
                         font: { size: 10 }
                       }
                     }
                   }
                 }" />
          </div>
        </div>
      </div>

      <!-- Detailed Violations Table -->
      <div class="bg-white rounded-xl shadow-sm p-4">
        <div class="flex items-center justify-between mb-3">
          <h3 class="text-sm font-semibold text-gray-900">Frequent Violations</h3>
          <span class="text-xs text-gray-500">Top {{ apiData.violations.frequent_violations.length }} violations</span>
        </div>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Violation</th>
                <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider w-20">Count</th>
                <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider w-24">Severity</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="violation in apiData.violations.frequent_violations" :key="violation.violation">
                <td class="px-3 py-2">
                  <div class="text-xs text-gray-900">{{ violation.violation }}</div>
                </td>
                <td class="px-3 py-2 whitespace-nowrap">
                  <div class="text-xs text-gray-500">{{ violation.count }}</div>
                </td>
                <td class="px-3 py-2 whitespace-nowrap">
                  <span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full"
                        :class="violation.count > 2 ? 'bg-red-100 text-red-800' : 
                               violation.count > 1 ? 'bg-yellow-100 text-yellow-800' : 
                               'bg-green-100 text-green-800'">
                    {{ violation.count > 2 ? 'High' : violation.count > 1 ? 'Medium' : 'Low' }}
                  </span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Industry Breakdown -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
        <div v-for="(violations, industry) in apiData.violations.by_industry" 
             :key="industry" 
             class="bg-white rounded-xl shadow-sm p-4">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">{{ industry }} Violations</h3>
          <ul class="space-y-1">
            <li v-for="violation in violations.slice(0, 3)" 
                :key="violation" 
                class="flex items-start space-x-2">
              <span class="flex-shrink-0 w-1.5 h-1.5 rounded-full bg-red-500 mt-1.5"></span>
              <span class="text-xs text-gray-600">{{ violation }}</span>
            </li>
          </ul>
          <p v-if="violations.length > 3" 
             class="mt-2 text-xs text-gray-500">
            And {{ violations.length - 3 }} more violations...
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.chart-container {
  position: relative;
  width: 100%;
}
</style>