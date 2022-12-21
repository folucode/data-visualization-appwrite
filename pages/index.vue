<template>
  <div>
    <div class="container-fluid">
      <DataCard tagline="Total Customers" :count="totalCustomers" />
      <DataCard tagline="Total Orders" :count="totalOrders" />
      <DataCard tagline="Total Revenue" :count="Math.round(totalRevenue)" />
    </div>

    <button class="button" @click="makeOrder()">Create Order</button>

    <h2>Orders</h2>

    <table>
      <tr>
        <th>Order ID</th>
        <th>Customer</th>
        <th>Price</th>
      </tr>
      <tr v-for="order in orders" :key="order.$id">
        <td>{{ order.$id }}</td>
        <td>{{ order.customer }}</td>
        <td>{{ order.price }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
import { Client, Account, Databases } from "appwrite";
import { faker } from "@faker-js/faker";

const client = new Client();
client
  .setEndpoint("http://localhost/v1") // Our Appwrite Endpoint
  .setProject("63a351045edffed66cfd");

const account = new Account(client);
const database = new Databases(client);

account.createAnonymousSession().then(
  (response) => {
    console.log(response);
  },
  (error) => {
    console.log(error);
  }
);

export default {
  name: "IndexPage",
  data() {
    return {
      orders: [],
      totalCustomers: 0,
      totalOrders: 0,
      totalRevenue: 0,
    };
  },
  methods: {
    async makeOrder() {
      await database.createDocument(
        "63a3511498700ae63186",
        "63a35118c60fbc24dd2b",
        "unique()",
        {
          customer: faker.name.fullName(),
          price: faker.finance.amount(),
        }
      );
    },
    async visualizeData() {
      const orderDetails = await database.listDocuments(
        "63a3511498700ae63186",
        "63a35118c60fbc24dd2b"
      );

      this.orders = orderDetails.documents;

      this.totalCustomers = orderDetails.total;
      this.totalOrders = orderDetails.total;
      this.totalRevenue = orderDetails.documents.reduce(
        (accumulator, currentValue) => accumulator + currentValue.price,
        0
      );
    },
  },
  mounted() {
    this.visualizeData();
    if (account.get !== null) {
      try {
        client.subscribe("documents", (response) => {
          this.visualizeData();
        });
      } catch (error) {
        console.log(error, "error");
      }
    }
  },
};
</script>

<style>
body {
  background-color: #252830;
  color: white;
}
.container-fluid {
  margin-left: 18%;
}

.chart {
  text-align: left;
  display: inline-block;
  width: 300px;
}

.chart-inner {
  background-color: rgba(233, 164, 164, 0.5);
}
.header {
  box-sizing: border-box;
  padding: 30px 15px;
  position: relative;
  text-shadow: 1px 1px 2px black;
}
.tagline {
  opacity: 0.75;
  text-transform: uppercase;
}
.count {
  display: inline-block;
  font-size: 32px;
  font-weight: 300;
  vertical-align: middle;
}

table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td,
th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

.button {
  background-color: #4caf50; /* Green */
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
}
</style>
