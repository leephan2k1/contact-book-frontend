<template>
  <div class="page">
    <h4>Thêm Liên hệ</h4>
    <ContactForm :contact="defaultFormValues" @submit:contact="createContact" />
    <p>{{ message }}</p>
  </div>
</template>

<script>
import { ref } from "vue";
import { useRouter } from "vue-router";

import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactForm,
  },
  setup() {
    const defaultFormValues = {
      doc: {
        name: "",
        address: "",
        phone: "",
        favorite: "",
        id: "",
      },
    };
    const message = ref("");
    const router = useRouter();

    const createContact = async (value) => {
      try {
        const res = await ContactService.create(value);
        if (res?.success) {
          message.value = "Liên hệ được tạo thành công.";
          setTimeout(() => {
            router.push({ name: "ContactBook" });
          }, 1000);
        }
      } catch (err) {
        console.log(err);
      }
    };

    return { createContact, defaultFormValues, message };
  },
};
</script>
