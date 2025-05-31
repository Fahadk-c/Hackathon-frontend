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
  LineController,
  Filler
} from 'chart.js'
import { LineChart } from 'vue-chart-3'
import { ref, onMounted } from 'vue'

// Register chart.js components
ChartJS.register(
  Title,
  Tooltip,
  Legend,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  LineController,
  Filler
)

// Sample API response data
const apiData = ref({
  
    "time_range": {
        "start": "2025-05-30T08:48:16.744000",
        "end": "2025-05-30T10:23:27.537000"
    },
    "total_messages_analyzed": 17,
    "compliance_scores": {
        "average_score": 0.8235294117647058,
        "score_distribution": {
            "0.0-0.2": 0,
            "0.2-0.4": 0,
            "0.4-0.6": 1,
            "0.6-0.8": 9,
            "0.8-1.0": 7
        },
        "trend": [
            {
                "date": "2025-05-30T00:00:00",
                "score": 0.8235294117647058
            }
        ]
    },
    "classifications": {
        "distribution": {
            "Review Needed": 10,
            "Non-compliant": 1,
            "Compliant": 6
        },
        "by_region": {
            "EASTERN": {
                "Review Needed": 10,
                "Non-compliant": 1,
                "Compliant": 6
            }
        },
        "by_industry": {
            "HEALTHCARE": {
                "Review Needed": 4,
                "Compliant": 6,
                "Non-compliant": 1
            },
            "FINANCIAL": {
                "Review Needed": 6
            }
        }
    },
    "violations": {
        "top_categories": [
            {
                "category": "Custom Requirements",
                "count": 5
            },
            {
                "category": "TCPA",
                "count": 4
            },
            {
                "category": null,
                "count": 2
            },
            {
                "category": "Telephone Consumer Protection Act",
                "count": 2
            }
        ],
        "frequent_violations": [
            {
                "violation": "Missing opt-out mechanism",
                "count": 3
            },
            {
                "violation": "Free medical services promotion",
                "count": 2
            },
            {
                "violation": "Lack of consent evidence",
                "count": 1
            },
            {
                "violation": "Missing opt-out information",
                "count": 1
            },
            {
                "violation": "Lack of explicit consent mention",
                "count": 1
            },
            {
                "violation": "Lack of consent confirmation for SMS messaging",
                "count": 1
            },
            {
                "violation": "Free Medical Rule",
                "count": 1
            },
            {
                "violation": "Lacks explicit consent verification",
                "count": 1
            },
            {
                "violation": "free medical rule",
                "count": 1
            },
            {
                "violation": "Free medical rule violation",
                "count": 1
            }
        ],
        "by_region": {
            "EASTERN": [
                "Lack of explicit consent mention",
                "Free medical rule violation",
                "Free Medical Rule",
                "Missing opt-out mechanism",
                "Free medical services promotion",
                "Lacks explicit consent verification",
                "Missing opt-out information",
                "free medical rule",
                "Lack of consent evidence",
                "Lack of consent confirmation for SMS messaging"
            ]
        },
        "by_industry": {
            "FINANCIAL": [
                "Lacks explicit consent verification",
                "Missing opt-out mechanism",
                "Lack of consent evidence",
                "Missing opt-out information",
                "Lack of explicit consent mention",
                "Lack of consent confirmation for SMS messaging"
            ],
            "HEALTHCARE": [
                "Free medical services promotion",
                "free medical rule",
                "Free medical rule violation",
                "Free Medical Rule"
            ]
        }
    },
    "regional_metrics": {
        "compliance_rates": {
            "EASTERN": 0.35294117647058826
        },
        "violation_patterns": {
            "EASTERN": [
                "Missing opt-out mechanism",
                "Free medical services promotion",
                "Lack of consent evidence",
                "Lacks explicit consent verification",
                "Missing opt-out information"
            ]
        },
        "trend": [
            {
                "region": "EASTERN",
                "date": "2025-05-01T00:00:00",
                "compliance_rate": 0.35294117647058826
            }
        ]
    },
    "industry_metrics": {
        "compliance_rates": {
            "FINANCIAL": 0.0,
            "HEALTHCARE": 0.5454545454545454
        },
        "violation_patterns": {
            "FINANCIAL": [
                "Missing opt-out mechanism",
                "Lack of consent evidence",
                "Lacks explicit consent verification",
                "Missing opt-out information",
                "Lack of explicit consent mention"
            ],
            "HEALTHCARE": [
                "Free medical services promotion",
                "Free Medical Rule",
                "free medical rule",
                "Free medical rule violation"
            ]
        },
        "trend": [
            {
                "industry": "FINANCIAL",
                "date": "2025-05-01T00:00:00",
                "compliance_rate": 0.0
            },
            {
                "industry": "HEALTHCARE",
                "date": "2025-05-01T00:00:00",
                "compliance_rate": 0.5454545454545454
            }
        ]
    },
    "temporal_metrics": {
        "daily_trend": [
            {
                "date": "2025-05-30T00:00:00",
                "compliance_rate": 0.35294117647058826,
                "average_score": 0.8235294117647058
            }
        ],
        "weekly_trend": [
            {
                "year": 2025,
                "week": 21,
                "compliance_rate": 0.35294117647058826,
                "average_score": 0.8235294117647058
            }
        ],
        "monthly_trend": [
            {
                "date": "2025-05-01T00:00:00",
                "compliance_rate": 0.35294117647058826,
                "average_score": 0.8235294117647058
            }
        ]
    }

});

const chartData = ref({
  labels: [] as string[],
  datasets: [
    {
      label: 'Compliance Rate',
      data: [] as number[],
      borderColor: 'rgba(59, 130, 246, 1)', // blue-500
      backgroundColor: 'rgba(59, 130, 246, 0.1)',
      tension: 0.3,
      fill: false
    },
    {
      label: 'Average Score',
      data: [] as number[],
      borderColor: 'rgba(16, 185, 129, 1)', // emerald-500
      backgroundColor: 'rgba(16, 185, 129, 0.1)',
      tension: 0.3,
      fill: false
    }
  ]
})

const chartOptions = ref({
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'top',
      labels: {
        color: '#64748b',
        font: {
          weight: 'bold'
        }
      },
    },
    tooltip: {
      callbacks: {
        label: (context: any) => {
          let label = context.dataset.label || '';
          if (label) label += ': ';
          if (context.parsed.y !== null) {
            label += context.dataset.label.includes('Rate') 
              ? `${(context.parsed.y * 100).toFixed(1)}%` 
              : context.parsed.y.toFixed(2);
          }
          return label;
        }
      }
    }
  },
  scales: {
    x: {
      ticks: { color: '#94a3b8' },
      grid: { color: '#e2e8f0' },
    },
    y: {
      ticks: { 
        color: '#94a3b8',
        callback: (value: any) => `${(value * 100)}%`
      },
      grid: { color: '#e2e8f0' },
      min: 0,
      max: 1
    },
  },
})

const stats = ref([
  {
    title: "Messages Analyzed",
    value: 0,
    change: 0,
    icon: "📊"
  },
  {
    title: "Compliance Rate",
    value: 0,
    change: 0,
    icon: "✅"
  },
  {
    title: "Top Violation",
    value: "",
    subvalue: "",
    icon: "⚠️"
  },
  {
    title: "Compliant Messages",
    value: 0,
    change: 0,
    icon: "👍"
  }
])

function processData(data: any) {
  // Process chart data
  const trendData = data.temporal_metrics.daily_trend;
  chartData.value.labels = trendData.map((item: any) => 
    new Date(item.date).toLocaleDateString('en-US', { month: 'short', day: 'numeric' })
  );
  chartData.value.datasets[0].data = trendData.map((item: any) => item.compliance_rate);
  chartData.value.datasets[1].data = trendData.map((item: any) => item.average_score);

  // Process stats cards
  stats.value[0].value = data.total_messages_analyzed;
  stats.value[1].value = data.regional_metrics.compliance_rates.EASTERN;
  stats.value[2].value = data.violations.top_categories[0].category;
  stats.value[2].subvalue = `${data.violations.top_categories[0].count} occurrences`;
  stats.value[3].value = data.classifications.distribution.Compliant;
}

onMounted(() => {
  // In a real app, you would fetch this from an API
  processData(apiData.value);
});
</script>

<template>
  <div class="min-h-screen bg-gray-50 p-3">
    <div v-if="loading" class="flex items-center justify-center h-screen">
      <div class="animate-spin rounded-full h-32 w-32 border-b-2 border-blue-500"></div>
    </div>

    <div v-else class="max-w-7xl mx-auto space-y-3">
      <!-- Header -->
      <div class="bg-white rounded-lg shadow-sm p-3">
        <h1 class="text-lg font-bold text-gray-900">Compliance Analytics Dashboard</h1>
        <p class="text-xs text-gray-600 mt-0.5">
          {{ new Date(apiData.time_range.start).toLocaleString() }} - 
          {{ new Date(apiData.time_range.end).toLocaleString() }}
        </p>
      </div>

      <!-- Critical Metrics -->
      <div class="grid grid-cols-2 md:grid-cols-4 gap-3">
        <div class="bg-white rounded-lg shadow-sm p-2.5">
          <div class="flex items-center justify-between">
            <h3 class="text-xs font-medium text-gray-500">Risk Level</h3>
            <span class="text-lg font-bold text-gray-700">{{ criticalMetrics.risk.level }}</span>
          </div>
          <div class="mt-1.5 space-y-1">
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
        <div class="bg-white rounded-lg shadow-sm p-2.5">
          <h3 class="text-xs font-medium text-gray-500">Average Score</h3>
          <div class="mt-1">
            <p class="text-lg font-bold" 
               :class="apiData.compliance_scores.average_score < 0.6 ? 'text-red-600' : 
                      apiData.compliance_scores.average_score < 0.8 ? 'text-yellow-600' : 'text-green-600'">
              {{ (apiData.compliance_scores.average_score * 100).toFixed(1) }}%
            </p>
          </div>
        </div>
        <div class="bg-white rounded-lg shadow-sm p-2.5">
          <h3 class="text-xs font-medium text-gray-500">Messages Analyzed</h3>
          <div class="mt-1">
            <p class="text-lg font-bold text-blue-600">{{ apiData.total_messages_analyzed }}</p>
          </div>
        </div>
        <div class="bg-white rounded-lg shadow-sm p-2.5">
          <h3 class="text-xs font-medium text-gray-500">Total Violations</h3>
          <div class="mt-1">
            <p class="text-lg font-bold text-red-600">{{ criticalMetrics.totalViolations }}</p>
          </div>
        </div>
      </div>

      <!-- Charts Grid -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-3">
        <!-- Score Distribution -->
        <div class="bg-white rounded-lg shadow-sm p-3">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Compliance Score Distribution</h3>
          <div class="h-[160px]">
            <Bar :data="complianceScoreChart" 
                 :options="{ 
                   ...baseChartOptions,
                   aspectRatio: 2,
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
                       ticks: { font: { size: 10 } }
                     },
                     x: {
                       ticks: { font: { size: 10 } }
                     }
                   }
                 }" />
          </div>
        </div>

        <!-- Classifications -->
        <div class="bg-white rounded-lg shadow-sm p-3">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Message Classifications</h3>
          <div class="h-[160px]">
            <Doughnut :data="classificationsChart" 
                      :options="{ 
                        ...baseChartOptions,
                        aspectRatio: 2,
                        plugins: {
                          ...baseChartOptions.plugins,
                          legend: {
                            position: 'right',
                            labels: { font: { size: 10 }, padding: 10, boxWidth: 10 }
                          }
                        }
                      }" />
          </div>
        </div>

        <!-- Top Violations -->
        <div class="bg-white rounded-lg shadow-sm p-3">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Top Violation Categories</h3>
          <div class="h-[160px]">
            <Bar :data="topViolationsChart" 
                 :options="{ 
                   ...baseChartOptions,
                   aspectRatio: 2,
                   plugins: {
                     ...baseChartOptions.plugins,
                     legend: { display: false }
                   },
                   scales: {
                     y: {
                       beginAtZero: true,
                       ticks: { font: { size: 10 } }
                     },
                     x: {
                       ticks: { font: { size: 10 } }
                     }
                   }
                 }" />
          </div>
        </div>

        <!-- Industry Compliance -->
        <div class="bg-white rounded-lg shadow-sm p-3">
          <h3 class="text-sm font-semibold text-gray-900 mb-2">Industry Compliance Rates</h3>
          <div class="h-[160px]">
            <Bar :data="industryComplianceChart" 
                 :options="{ 
                   ...baseChartOptions,
                   aspectRatio: 2,
                   plugins: {
                     ...baseChartOptions.plugins,
                     legend: { display: false }
                   },
                   scales: {
                     y: {
                       beginAtZero: true,
                       max: 100,
                       ticks: { 
                         font: { size: 10 },
                         callback: (value) => `${value}%`
                       }
                     },
                     x: {
                       ticks: { font: { size: 10 } }
                     }
                   }
                 }" />
          </div>
        </div>
      </div>

      <!-- Detailed Violations Table -->
      <div class="bg-white rounded-lg shadow-sm p-3">
        <div class="flex items-center justify-between mb-2">
          <h3 class="text-sm font-semibold text-gray-900">Frequent Violations</h3>
          <span class="text-xs text-gray-500">Top {{ apiData.violations.frequent_violations.length }} violations</span>
        </div>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-2 py-1.5 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Violation</th>
                <th class="px-2 py-1.5 text-left text-xs font-medium text-gray-500 uppercase tracking-wider w-20">Count</th>
                <th class="px-2 py-1.5 text-left text-xs font-medium text-gray-500 uppercase tracking-wider w-24">Severity</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="violation in apiData.violations.frequent_violations" :key="violation.violation">
                <td class="px-2 py-1.5">
                  <div class="text-xs text-gray-900">{{ violation.violation }}</div>
                </td>
                <td class="px-2 py-1.5 whitespace-nowrap">
                  <div class="text-xs text-gray-500">{{ violation.count }}</div>
                </td>
                <td class="px-2 py-1.5 whitespace-nowrap">
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
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-3">
        <div v-for="(violations, industry) in apiData.violations.by_industry" 
             :key="industry" 
             class="bg-white rounded-lg shadow-sm p-3">
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
             class="mt-1.5 text-xs text-gray-500">
            And {{ violations.length - 3 }} more violations...
          </p>
        </div>
      </div>
    </div>
  </div>
</template>