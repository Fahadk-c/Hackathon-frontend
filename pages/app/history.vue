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
  <div class="space-y-6 p-6 max-w-6xl mx-auto">
    <Card class="p-6 shadow-md border border-border bg-background dark:bg-slate-900 rounded-xl">
      <CardHeader class="pb-4">
        <CardTitle class="text-lg font-semibold text-foreground">
          📈 Compliance Analytics Dashboard
        </CardTitle>
        <CardDescription class="text-muted-foreground">
          {{
            new Date(apiData.time_range.start).toLocaleDateString() === new Date(apiData.time_range.end).toLocaleDateString()
              ? `Data for ${new Date(apiData.time_range.start).toLocaleDateString()}`
              : `Data from ${new Date(apiData.time_range.start).toLocaleDateString()} to ${new Date(apiData.time_range.end).toLocaleDateString()}`
          }}
        </CardDescription>
      </CardHeader>
      <CardContent>
        <div class="h-96 mb-8">
          <LineChart :chart-data="chartData" :options="chartOptions" />
        </div>
        
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          <Card v-for="(stat, index) in stats" :key="index" class="p-4 shadow-sm border-border">
            <CardHeader class="flex flex-row items-center justify-between pb-2">
              <CardTitle class="text-sm font-medium text-muted-foreground">
                {{ stat.title }}
              </CardTitle>
              <span class="text-2xl">{{ stat.icon }}</span>
            </CardHeader>
            <CardContent>
              <div class="text-2xl font-bold">
                {{ typeof stat.value === 'number' && stat.title.includes('Rate') 
                  ? `${(stat.value * 100).toFixed(1)}%` 
                  : stat.value }}
              </div>
              <p v-if="stat.subvalue" class="text-xs text-muted-foreground mt-1">
                {{ stat.subvalue }}
              </p>
            </CardContent>
          </Card>
        </div>
      </CardContent>
    </Card>
  </div>
</template>