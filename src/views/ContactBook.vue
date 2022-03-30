<template>
  <div class="page row">
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>
    <div class="mt-3 col-md-6">
      <h4>
        Danh bạ
        <i class="fas fa-address-book"></i>
      </h4>
      <ContactList
        v-if="filteredContactsCount() > 0"
        :contacts="filteredContacts()"
        v-model:activeIndex="activeIndex"
      />
      <p v-else>Không có liên hệ nào.</p>

      <div class="mt-3 row justify-content-around align-items-center">
        <button class="btn btn-sm btn-primary" @click="refreshList()">
          <i class="fas fa-redo"></i> Làm mới
        </button>

        <button class="btn btn-sm btn-success" @click="goToAddContact">
          <i class="fas fa-plus"></i> Thêm mới
        </button>

        <button class="btn btn-sm btn-danger" @click="removeAllContacts">
          <i class="fas fa-trash"></i> Xóa tất cả
        </button>
      </div>
    </div>
    <div class="mt-3 col-md-6">
      <div v-if="activeContact()">
        <h4>
          Chi tiết Liên hệ
          <i class="fas fa-address-card"></i>
        </h4>
        <ContactCard :contact="activeContact()" />
        <router-link
          :to="{
            name: 'EditContact',
            params: { id: activeContact()?.id },
          }"
        >
          <span class="mt-2 badge badge-warning">
            <i class="fas fa-edit"></i> Hiệu chỉnh</span
          >
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch, onMounted } from "vue";
import { useRouter } from "vue-router";

import ContactCard from "@/components/ContactCard.vue";
import InputSearch from "@/components/InputSearch.vue";
import ContactList from "@/components/ContactList.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactCard,
    InputSearch,
    ContactList,
  },
  setup() {
    const contacts = ref([]);
    const activeIndex = ref(-1);
    const searchText = ref("");
    const router = useRouter();

    watch(searchText, () => {
      activeIndex.value = -1;
    });

    const contactStrings = () => {
      return contacts.value.map((contact) => {
        const { name, email, address, phone } = contact;
        return [name, email, address, phone].join("");
      });
    };

    const filteredContacts = () => {
      if (!searchText.value) return contacts.value;
      return contacts.value.filter((contact, index) =>
        contactStrings()[index].includes(searchText.value)
      );
    };

    const activeContact = () => {
      if (activeIndex.value < 0) return null;
      return contacts.value[activeIndex.value];
    };

    const filteredContactsCount = () => {
      return filteredContacts()?.length;
    };

    const retrieveContacts = async () => {
      try {
        const res = await ContactService.getAll();
        contacts.value = res?.docs;
      } catch (err) {
        console.log(err);
      }
    };

    const refreshList = () => {
      retrieveContacts();
      activeIndex.value = -1;
    };

    const removeAllContacts = async () => {
      if (confirm("Bạn muốn xóa tất cả Liên hệ?")) {
        try {
          await ContactService.deleteAll();
          refreshList();
        } catch (error) {
          console.log(error);
        }
      }
    };

    const goToAddContact = () => {
      router.push({ name: "AddContact" });
    };

    onMounted(() => {
      refreshList();
    });

    return {
      contactStrings,
      filteredContacts,
      searchText,
      activeIndex,
      activeContact,
      filteredContactsCount,
      goToAddContact,
      removeAllContacts,
    };
  },
};
</script>

<style scoped>
.page {
  text-align: left;
  max-width: 750px;
}
</style>
