<style lang="postcss">
  :global(.social-button) {
    font-size: 14px;
    align-self: stretch;
    background-color: var(--paper) !important;
  }

  .container {
    background: linear-gradient(
      136.71deg,
      var(--auth-background-light) 21.32%,
      var(--auth-background-dark) 96.51%
    );
    grid-template-columns: 1fr;
    grid-template-rows: 1fr;

    display: flex;
    align-items: center;
    justify-content: center;

    .link {
      font-size: 14px;
      text-decoration: underline;
      color: var(--link);

      display: flex;
      align-items: center;
    }

    form {
      padding: 60px 70px;
      width: 60%;
      height: 654px;
      background-color: var(--background);
      box-shadow: 0px 24px 42px rgba(0, 0, 0, 0.08);
      border-radius: 16px;
      max-height: 654px;
      max-width: 640px;
      align-self: center;

      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;

      @media (--mobile) {
        max-height: 100%;
        height: 100%;
        flex: 1;
        align-self: stretch;
        border-radius: 0;
        padding: 0;
      }

      .container-options {
        align-self: stretch;
        margin: 0 10%;

        margin-top: 8px;
        display: flex;
        flex-direction: row;
        justify-content: space-between;

        a:first-child {
          text-align: left;
        }
      }

      :global(.input-wrapper) {
        grid-template-columns: min-content 1fr;
      }
    }
  }
</style>

<script lang="ts">
  import supabase from '../../lib/db';
  import {SvgFacebook, SvgKey, SvgLogo, SvgMail} from '../../utils/icons';
  import Button from '../../layouts/button.svelte';
  import {_} from 'svelte-i18n';
  import EditText from '../../layouts/edit-text.svelte';
  import {onMount} from 'svelte';

  let loading = false;
  let email: string;
  let password: string;

  const fbBtnStyle = `
    align-self: stretch;
    border-radius: 4px;
    background-color: #375A93;
    color: var(--textContrast);
    margin-bottom: 12px;
    margin: 0 10%;

    display:flex;
    flex-direction: row;
    justify-content: center;
    align-items:center;
  `;

  onMount(async () => {
    // if ($user) {
    //   await replace('/');
    // }
  });

  const onChangeEmail = (e: CustomEvent) => {
    email = e.detail;
  };

  const onChangePassword = (e: CustomEvent) => {
    password = e.detail;
  };

  const handleLogin = async () => {
    try {
      loading = true;
      const {error} = await supabase.auth.signIn({
        email,
        password,
      });

      if (error) {
        throw error;
      }
    } catch (error) {
      console.log(error);
    } finally {
      loading = false;
    }
  };

  const signInWithFacebook = async () => {
    try {
      loading = true;
      const {user, session, error, url} = await supabase.auth.signIn({
        provider: 'facebook',
      });

      //! Caveat: Run below code when supabase does not work as intended.
      if (!user && !error && !session) {
        window.open(url as string);
        window.close();
      }

      if (error) {
        throw error;
      }
    } catch (error) {
      console.log(error);
    } finally {
      loading = false;
    }
  };
</script>

<div class="container">
  <form>
    <img src={SvgLogo} alt="logo" />
    <h1 style="margin-bottom: 60px;">{$_('login')}</h1>
    <EditText
      containerStyle="width: 80%; margin-bottom: 8px"
      inputStyle="font-size: 14px;"
      type="email"
      placeholder={$_('auth.sign-in.email_hint')}
      on:changed={onChangeEmail}
    >
      <img
        slot="leftElement"
        style="margin: 0 16px;"
        src={SvgMail}
        alt="mail"
      />
    </EditText>
    <EditText
      containerStyle="width: 80%;"
      inputStyle="font-size: 14px;"
      type="password"
      placeholder={$_('auth.sign-in.pw_hint')}
      on:changed={onChangePassword}
    >
      <img slot="leftElement" src={SvgKey} alt="mail" style="margin: 0 16px;" />
    </EditText>
    <div class="container-options">
      <a class="link" href="/auth/sign-up"
        >{$_('auth.sign-in.do_not_have_account')}?</a
      >
      <a class="link" href="/auth/find-pw"
        >{$_('auth.sign-in.forgot_your_password')}?</a
      >
    </div>
    <Button
      on:click={handleLogin}
      primary
      style="align-self: stretch; margin: 40px 10% 10px 10%"
      disabled={loading}
      loading={loading}
    >
      <div class="text body3" style="font-weight: 500; color: white;">
        {$_('sign_in')}
      </div>
    </Button>
    <Button style={fbBtnStyle} on:click={signInWithFacebook}>
      <img style="margin-right: 15px;" src={SvgFacebook} alt="facebook" />
      <div class="text body3" style="font-weight: 500; color: white;">
        {$_('auth.sign-in.sign_in_with_facebook')}
      </div>
    </Button>
    <a
      class="link"
      href="/#"
      style="
        margin-top: 14px;
        text-decoration: underline !important;
      "
    >
      {$_('auth.sign-in.look_around')}
    </a>
  </form>
</div>
