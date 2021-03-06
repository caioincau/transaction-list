<template>
  <section class="transaction-view">
    <ul class="transaction-list">
      <li class="f-row transaction-list__title">
        <span role="heading" class="transaction-list__date">Transaction date</span>
        <span role="heading" class="f-col">Description</span>
        <span role="heading" class="transaction-list__amount">Amount</span>
        <span role="heading" class="transaction-list__actions">Actions</span>
      </li>
      <transition-group name="fade">
        <li class="f-row transaction-list__item" v-for="(item, key) in sorted_transactions" :key="key">
          <span class="transaction-list__date">
            <span class="transaction-list__mobile-title">Date: </span>
            <span class="transaction-list__date-value">{{ item.createdAt | date }}</span>
          </span>
          <span class="f-col transaction-list__description.f-col"><span class="transaction-list__mobile-title">Description: </span>{{ item.description }}</span>
          <span class="transaction-list__amount"><span class="transaction-list__mobile-title">Amount: </span>R$ {{ item.value }}</span>
          <span role="button" class="transaction-list__actions"><a class="transaction-list__actions-remove" @click="$_remove(item)">Remove</a></span>
        </li>
        <li class="f-row transaction-list__item" key="-2">
          <span class="transaction-list__date">
          </span>
          <span class="f-col transaction-list__description.f-col">Incoming</span>
          <span class="transaction-list__amount">R$ {{ positive_amount }}</span>
          <span role="button" class="transaction-list__actions"></span>
        </li>
        <li class="f-row transaction-list__item" key="-1">
          <span class="transaction-list__date">
          </span>
          <span class="f-col transaction-list__description.f-col">Outcoming</span>
          <span class="transaction-list__amount --negative">R$ {{ negative_amount }}</span>
          <span role="button" class="transaction-list__actions"></span>
        </li>
        <li class="f-row transaction-list__item" key="-3">
          <span class="transaction-list__date">
          </span>
          <span class="f-col transaction-list__description.f-col">Total</span>
          <span class="transaction-list__amount">R$ {{ total_amount }}</span>
          <span role="button" class="transaction-list__actions"></span>
        </li>
      </transition-group>
    </ul>
  </section>
</template>
<style lang="less">
@import '../../assets/styles/main';

@actions-size: 100px;
@base-item-size: 150px;
.transaction-view {
  max-width: 920px;
  margin: 0 auto;
}
.transaction-list {
  list-style: none;

  &__amount {
    flex-basis: @base-item-size;
    text-align: left;
    white-space: nowrap;

    &.--negative {
      color: @red;
    }
  }

  &__item {
    border-bottom: 1px solid @gray;
    padding: @exs-spacer;

    span {
      max-width: 100%;
      overflow: hidden;
      text-overflow: ellipsis;
    }
  }

  &__date {
    flex-basis: @base-item-size;
    text-align: left;
  }

  &__actions {
    flex-basis: @actions-size;
    text-align: left;
    a {
      cursor: pointer;
    }
  }

  &__total {
    float: right;
    text-align: left;
    width: @base-item-size + @exs-spacer + @actions-size;
    display: block;
  }

  &__title {
    padding: @exs-spacer;
    span {
      font-weight: bold;
    }
  }

  &__mobile-title {
    display: none;
  }
}

@media only screen and (max-width: 768px) {
  .transaction-list {
    &__item {
      flex-wrap: wrap;
      border: 1px solid @gray;
      margin-top: @xs-spacer;
      box-shadow: @base-shadow;
      span {
        flex-basis: 100%;
      }
    }

    &__title {
      display: none;
    }

    &__description.f-col {
      flex-basis: 100%;
    }

    &__total {
      float: left;
    }

    &__mobile-title {
      display: inline;
      font-weight: bold;
    }
  }
}
</style>

<script>
import dateFilter from '@/filters/date'
import moment from 'moment'

export default {
  filters: {
    date: dateFilter
  },
  computed: {
    total_amount () {
      if (!this.transactions.length) return 0
      let total = this.transactions.reduce((acumulador, actual) =>
        acumulador + actual.valueAsNumber(), 0)
      return Number(total.toFixed(2))
    },
    negative_amount () {
      const total = this.transactions.reduce((acc, actual) => {
        const valueAsNumber = actual.valueAsNumber()
        return valueAsNumber < 0 ? acc + valueAsNumber : acc
      }, 0)
      return Number(total.toFixed(2))
    },
    positive_amount () {
      const total = this.transactions.reduce((acc, actual) => {
        const valueAsNumber = actual.valueAsNumber()
        return valueAsNumber > 0 ? acc + valueAsNumber : acc
      }, 0)
      return Number(total.toFixed(2))
    },
    sorted_transactions () {
      let sorted = Array.from(this.transactions)
      return sorted.sort((a, b) => {
        return moment(b.createdAt).format('X') - moment(a.createdAt).format('X')
      })
    }
  },
  methods: {
    $_remove (item) {
      const index = this.transactions.indexOf(item)
      if (index > -1) {
        this.transactions.splice(index, 1)
        localStorage.setItem('transactions', JSON.stringify(this.transactions))
      }
    }
  },
  props: {
    transactions: {
      type: Array,
      default: () => [],
      description: 'Transaction list'
    }
  }
}
</script>
