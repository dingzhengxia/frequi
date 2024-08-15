<script setup lang="ts">
import { Trade } from '@/types';
import {PropType} from "vue-demi";

type modes = 'default' | 'total' | 'summary' | 'realized';

const props = defineProps({
  trade: { required: true, type: Object as () => Trade },
  mode: {
    required: false,
    default: 'default',
    type: String as PropType<modes>,
  },
});

const modeDescs: { [key in modes]: string } = {
  default: 'Current profit',
  total: 'Total profit',
  summary: 'Summary profit',
  realized: 'Realized profit',
};

const profitRatio = computed<number | undefined>(() => {
  switch (props.mode) {
    case 'default':
      return props.trade.profit_ratio;
    case 'total':
      return props.trade.total_profit_ratio;
    case 'summary':
      return props.trade.summary_profit_ratio;
    case 'realized':
      return props.trade.realized_profit_ratio;
    default:
      return undefined;
  }
});
const profitAbs = computed<number | undefined>(() => {
  switch (props.mode) {
    case 'default':
      return props.trade.profit_abs;
    case 'total':
      return props.trade.total_profit_abs;
    case 'summary':
      return props.trade.summary_profit_abs;
    case 'realized':
      return props.trade.realized_profit;
    default:
      return undefined;
  }
});
const profitDesc = computed((): string => {
  let profit = `${modeDescs[props.mode]}: ${
    profitRatio.value ? formatPercent(profitRatio.value) : ''
  } (${profitAbs.value})`;
  profit += `\nOpen since: ${timestampms(props.trade.open_timestamp)}`;
  return profit;
});
</script>

<template>
  <ProfitPill
    :profit-ratio="profitRatio"
    :profit-abs="profitAbs"
    :profit-desc="profitDesc"
    :stake-currency="trade.quote_currency || 'USDT'"
  />
</template>
