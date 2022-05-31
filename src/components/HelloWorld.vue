<template>

  <v-app>
    <v-app-bar app color="white" elevation="0">
      <span>
        GTG ERP
      </span>

      <v-spacer></v-spacer>

      <v-btn class="white--text py-5 px-5" color="blue lighten-1" rounded elevation="0">
        <v-avatar size="32">
          <img src="https://cdn.vuetifyjs.com/images/john.jpg" alt="John">
        </v-avatar>
        <span class="mr-2">کاربر مدیر</span>
      </v-btn>

      <template v-slot:extension>
        <v-tabs v-model="tab" align-with-title hide-slider>
          <v-tab v-for="tab in mainTabs" :key="tab.id" :ripple="false" class="mr-0 d-flex justify-space-between"
            style="min-width:180px;">
            <span>{{ tab.text }}</span>
            <v-icon v-if="mainTabs.length > 1" small @click.stop="closeTab(tab.id)">mdi-close</v-icon>
          </v-tab>
        </v-tabs>
      </template>
    </v-app-bar>

    <v-main>
      <v-tabs-items v-model="tab">
        <v-tab-item>
          <v-card color="blue lighten-5" class="rounded-0 pa-2">
            <div class="d-flex align-center">
              <v-icon color=" blue darken-2">mdi-home</v-icon>
              <v-breadcrumbs :items="breadcrumbsManagment">
                <template v-slot:divider>
                  <v-icon>mdi-chevron-left</v-icon>
                </template>
              </v-breadcrumbs>
            </div>
            <v-card elevation="2" class="px-5 py-2 my-2 d-flex align-center justify-space-between">
              <span class="h4">لیست کارها</span>
              <div>
                <v-btn color="primary" elevation="2" large class="ml-2">
                  <v-icon>mdi-magnify</v-icon>
                  جستجو
                </v-btn>
                <v-btn color="primary" elevation="2" large>
                  <v-icon>mdi-plus</v-icon>
                  ایجاد کار
                </v-btn>
              </div>
            </v-card>
            <v-card class="d-flex align-center justify-space-between pl-5">
              <v-tabs class="rounded">
                <v-tab style="letter-spacing:0;">متعلق به من</v-tab>
                <v-tab style="letter-spacing:0;">متعلق به بخش من</v-tab>
                <v-tab style="letter-spacing:0;">ایجاد شده توسط من</v-tab>
                <v-tab style="letter-spacing:0;">دنبال شده‌ها</v-tab>
                <v-tab style="letter-spacing:0;">همه</v-tab>
              </v-tabs>
              <v-btn-toggle v-model="toggle_exclusive" dense>
                <v-btn>
                  <v-icon>mdi-apps</v-icon>
                </v-btn>
                <v-btn>
                  <v-icon>mdi-format-list-bulleted-square</v-icon>
                </v-btn>
              </v-btn-toggle>
            </v-card>
            <v-card class="my-2 rounded">
              <v-data-table no-data-text="اطلاعاتی موجود نیست"
                :footer-props="{ 'items-per-page-text': 'تعداد در هر صفحه' }" :headers="header" :items="data"
                :items-per-page="5">

                <template v-slot:item="{ item }">
                  <tr>
                    <td>
                      <span>{{ item.name }}</span>
                    </td>
                    <td>
                      {{ item.type }}
                    </td>
                    <td>
                      <div class="d-flex align-center">
                        <v-avatar class="ml-2">
                          <img :src="item.sender.img" size="12">
                        </v-avatar>
                        <div>
                          <div>{{ item.sender.fullName }}</div>
                          <div>{{ item.sender.sub }}</div>
                        </div>
                      </div>
                    </td>
                    <td style="min-width:100px" class="py-2 px-0">
                      <div class="d-flex align-center">
                        <v-avatar class="ml-2">
                          <img :src="item.reciever.img" size="12">
                        </v-avatar>
                        <div>
                          <div>{{ item.reciever.fullName }}</div>
                          <div>{{ item.reciever.sub }}</div>
                        </div>
                      </div>
                    </td>
                    <td>
                      {{ toLocale(item.timeout) }}
                    </td>
                    <td>
                      {{ toLocale(item.created_at) }}
                    </td>
                    <td>
                      {{ toLocale(item.last_edit) }}
                    </td>
                    <td>
                      {{ item.edit ? "بله" : "خیر" }}
                    </td>
                    <td>
                      <v-chip v-if="item.status == 0" color="blue" class="white--text">
                        جدید
                      </v-chip>
                      <v-chip v-else-if="item.status == 1" color="orange" class="white--text">
                        در حال انجام
                      </v-chip>
                    </td>
                  </tr>
                </template>
              </v-data-table>
            </v-card>
          </v-card>

        </v-tab-item>
        <v-tab-item>
          <v-card color="blue lighten-5" class="rounded-0 pa-2">
            <div class="d-flex align-center">
              <v-icon color=" blue darken-2">mdi-home</v-icon>
              <v-breadcrumbs :items="breadcrumbsCustomers">
                <template v-slot:divider>
                  <v-icon>mdi-chevron-left</v-icon>
                </template>
              </v-breadcrumbs>
            </div>
            <v-card>
              <v-tabs style="border-bottom: 1px solid #ccc;">
                <v-tab style="letter-spacing: 0;">اطلاعات مشتری</v-tab>
                <v-tab style="letter-spacing: 0;">آدرس‌ها</v-tab>
                <v-tab style="letter-spacing: 0;">کارمندان</v-tab>
                <v-tab style="letter-spacing: 0;">کارت‌های بازرگانی</v-tab>
                <v-tab style="letter-spacing: 0;">وکالت نامه‌ها</v-tab>
                <v-tab style="letter-spacing: 0;">نام کاربری سامانه‌ها</v-tab>
              </v-tabs>
              <div class="pa-2">
                <v-container fluid>
                  <v-row align="center">
                    <v-col cols="4">
                      <div class="d-flex align-center">
                        <span>نوع مشتری :</span>
                        <v-radio-group v-model="row" row>
                          <v-radio label="حقیقی" value="radio-2"></v-radio>
                          <v-radio label="حقوقی" value="radio-1"></v-radio>
                        </v-radio-group>
                      </div>
                    </v-col>
                    <v-spacer></v-spacer>
                    <v-col cols="3">
                      <v-text-field value="041" outlined>
                        <template #label>
                          کد مشتری
                          <span class="red--text"><strong>* </strong></span>
                        </template>
                      </v-text-field>
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col cols="3">
                      <v-text-field value="041" outlined>
                        <template #label>
                          نام
                          <span class="red--text"><strong>* </strong></span>
                        </template>
                      </v-text-field>
                    </v-col>
                    <v-col cols="3">
                      <v-text-field value="041" outlined>
                        <template #label>
                          نام انگلیسی
                          <span class="red--text"><strong>* </strong></span>
                        </template>
                      </v-text-field>
                    </v-col>
                    <v-col cols="3">
                      <v-text-field value="041" outlined>
                        <template #label>
                          کد ملی
                          <span class="red--text"><strong>* </strong></span>
                        </template>
                      </v-text-field>
                    </v-col>
                    <v-col cols="3">
                      <v-text-field value="041" outlined>
                        <template #label>
                          نوع فعالیت
                          <span class="red--text"><strong>* </strong></span>
                        </template>
                      </v-text-field>
                    </v-col>
                  </v-row>
                  <v-row class="pa-3 d-flex align-center">
                    <v-btn color="blue lighten-1" class="white--text" elevation="0">
                      <v-icon>mdi-upload</v-icon>
                      آپلود فایل
                    </v-btn>
                    <span class="mr-2">0 فایل</span>
                  </v-row>
                </v-container>
              </div>
              <v-divider></v-divider>
              <v-container fluid>
                <v-row class="pa-4">
                  <v-spacer></v-spacer>
                  <v-btn disabled>ذخیره</v-btn>
                </v-row>
              </v-container>
            </v-card>
          </v-card>
        </v-tab-item>
      </v-tabs-items>
    </v-main>
  </v-app>

</template>

<script>
export default {
  name: 'HelloWorld',
  methods: {
    toLocale(date) {
      return (
        new Date(date).toLocaleDateString("fa-IR") + " " + new Date(date).toLocaleTimeString("fa-IR")
      )
    },
    closeTab(id) {
      this.mainTabs = this.mainTabs.filter(tab => tab.id != id)
    }
  },
  data: () => ({
    items: [
      'مدیریت کارها', "مشتریان"
    ],
    tab: null,
    mainTabs: [
      {
        id: 1,
        text: 'مدیریت کارها',
      },
      {
        id: 2,
        text: 'مشتریان',
      },
    ],
    breadcrumbsManagment: [
      {
        text: 'داشبورد',
        disabled: false,
        href: 'breadcrumbs_dashboard',
      },
      {
        text: 'مدیریت کارها',
        disabled: false,
        href: 'breadcrumbs_link_1',
      },
    ],
    breadcrumbsCustomers: [
      {
        text: 'داشبورد',
        disabled: false,
        href: 'breadcrumbs_dashboard',
      },
      {
        text: 'مشتریان',
        disabled: false,
        href: 'breadcrumbs_link_1',
      },
      {
        text: 'ویرایش مشتری حسن جعفری',
        disabled: false,
        href: 'breadcrumbs_link_1',
      },
    ],
    header: [
      {
        text: 'نام پرونده',
        value: 'name',
        sortable: false,
      },
      {
        text: 'نوع اقدام',
        value: 'type',
        sortable: false,
      },
      {
        text: 'فرستنده',
        value: 'sender',
        sortable: false,
      },
      {
        text: 'گیرنده',
        value: 'reciever',
        sortable: false,
      },
      {
        text: 'مهلت پاسخگویی',
        value: 'timeout',
        sortable: false,
      },
      {
        text: 'زمان ایجاد',
        value: 'created_at',
        sortable: false,
      },
      {
        text: 'آخرین ویرایش',
        value: 'last_edit',
        sortable: false,
      },
      {
        text: 'اصلاحیه',
        value: 'edit',
        sortable: false,
      },
      {
        text: 'وضعیت',
        value: 'status',
        sortable: false,
      },
    ],
    data: [
      {
        name: 'گیربکس',
        type: null,
        sender: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        reciever: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        timeout: "2020-07-10 15:00:00.000",
        created_at: "2020-07-10 15:00:00.000",
        last_edit: "2020-07-10 15:00:00.000",
        edit: false,
        status: 0
      },
      {
        name: 'پرونده واردات واکسن',
        type: null,
        sender: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        reciever: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        timeout: "2020-07-10 15:00:00.000",
        created_at: "2020-07-10 15:00:00.000",
        last_edit: "2020-07-10 15:00:00.000",
        edit: false,
        status: 0

      },
      {
        name: 'واردات ماشین',
        type: null,
        sender: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        reciever: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        timeout: "2020-07-10 15:00:00.000",
        created_at: "2020-07-10 15:00:00.000",
        last_edit: "2020-07-10 15:00:00.000",
        edit: false,
        status: 0
      },
      {
        name: 'کاسه نمد',
        type: null,
        sender: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        reciever: {
          img: "https://randomuser.me/api/portraits/men/78.jpg",
          fullName: "کاربر مدیر",
          sub: "رفع تعهدات مالی"
        },
        timeout: "2020-07-10 15:00:00.000",
        created_at: "2020-07-10 15:00:00.000",
        last_edit: "2020-07-10 15:00:00.000",
        edit: false,
        status: 1

      },
    ]
  })
}
</script>
