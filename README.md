# TP-Testes-de-Software

Trabalho Prático sobre Testes de Software - PDS
Aluno: Pedro Fonseca Wildemberg
Matrículo: 2017014359

freeCodeCamp/cypress/integration/user/report-user.js 

``` javascript

describe('Report User', () => {
  beforeEach(() => {
    cy.exec('npm run seed');
    cy.login();
  });
  it('should be possible to report a user from their profile page', () => {
    // Since going to a user page initially generates a 404, we have to ignore
    // status codes on that request
    cy.visit('/twaha', { failOnStatusCode: false });
    // The following line is only required if you want to test it in development
    // cy.contains('Preview custom 404 page').click();
    cy.contains("Flag This User's Account for Abuse").click();
    cy.contains("Do you want to report twaha's portfolio for abuse?");
    cy.get('[id=report-user-textarea]').type('Some details');
    cy.contains('Submit the report').click();
    cy.location().should(loc => {
      expect(loc.pathname).to.eq('/learn');
    });
    cy.contains('A report was sent to the team with foo@bar.com in copy');
  });
});

```

O teste acima consiste em um teste de integração e testa o processo de reportar um usuário antravés da sua página. Primeiramente acessamos a página do usuário "twaha", em seguida clicamos no botão para reportar o portfólio de "twaha". Depois adicionamos detalhes no campo de texto e clicamos no botão para submeter o relatório escrito.

freeCodeCamp/cypress/integration/settings/username-change.js

``` javascript

describe('Username input field', () => {
  beforeEach(() => {
    cy.login();
  });

  function goToSettings() {
    cy.visit('/settings');

    // Setting aliases here
    cy.get('input[name=username-settings]').as('usernameInput');
    cy.get('form#usernameSettings').as('usernameForm');
  }

  it('Should be possible to type', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('twaha', { force: true })
      .should('have.attr', 'value', 'twaha');
  });

  it('Should show message when validating name', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('twaha', { force: true });

    cy.contains('Validating username...')
      .should('have.attr', 'role', 'alert')
      // We are checking for classes here to check for proper styling
      // This will be replaced with Percy in the future
      .should('have.class', 'alert alert-info');
  });

  it('Should show username is available if it is', () => {
    goToSettings();
     cy.get('@usernameInput')
      .clear({ force: true })
      .type('brad', { force: true });

    cy.contains('Username is available')
      .should('be.visible')
      .should('have.attr', 'role', 'alert')
      // We are checking for classes here to check for proper styling
      // This will be replaced with Percy in the future
      .should('have.class', 'alert alert-success');
  });

  it('Should info message if username is available', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('mrugesh', { force: true });

    cy.contains(
      'Please note, changing your username will also change ' +
        'the URL to your profile and your certifications.'
    )
      .should('be.visible')
      .should('have.attr', 'role', 'alert')
      // We are checking for classes here to check for proper styling
      // This will be replaced with Percy in the future
      .should('have.class', 'alert alert-info');
  });

  // eslint-disable-next-line
  it('Should be able to click the `Save` button if username is avalable', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('oliver', { force: true });

    cy.get('@usernameForm').within(() => {
      cy.contains('Save').should('not.be.disabled');
    });
  });

  it('Should show username is unavailable if it is', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('twaha', { force: true });

    cy.contains('Username not available')
      .should('be.visible')
      .should('have.attr', 'role', 'alert')
      // We are checking for classes here to check for proper styling
      // This will be replaced with Percy in the future
      .should('have.class', 'alert alert-warning');
  });

  // eslint-disable-next-line
  it('Should not be possible to click the `Save` button if username is unavailable', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('twaha', { force: true });

    cy.contains('Username is available').should('not.exist');
    cy.contains('Username not available').should('not.exist');
    cy.contains(
      'Please note, changing your username will also change ' +
        'the URL to your profile and your certifications.'
    ).should('not.exist');

    cy.get('@usernameForm').contains('Save').should('be.disabled');
  });

  it('Should not show anything if user types their current name', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('developmentuser', { force: true });

    cy.get('@usernameForm').contains('Save').should('be.disabled');
  });

  // eslint-disable-next-line max-len
  it('Should not be possible to click the `Save` button if user types their current name', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('developmentuser', { force: true });

    cy.get('@usernameForm').contains('Save').should('be.disabled');
  });

  it('Should show warning if username includes invalid character', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('Quincy Larson', { force: true });

    cy.contains('Username "Quincy Larson" contains invalid characters')
      .should('be.visible')
      .should('have.attr', 'role', 'alert')
      // We are checking for classes here to check for proper styling
      // This will be replaced with Percy in the future
      .should('have.class', 'alert alert-danger');
  });

  // eslint-disable-next-line max-len
  it('Should not be able to click the `Save` button if username includes invalid character', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('Quincy Larson', { force: true });

    cy.get('@usernameForm').contains('Save').should('be.disabled');
  });

  it('Should change username if `Save` button is clicked', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('quincy', { force: true });

    cy.contains('Username is available');

    cy.get('@usernameForm').contains('Save').click({ force: true });
    cy.contains('Account Settings for quincy').should('be.visible');

    cy.resetUsername();
  });

  it('Should change username with uppercase characters if `Save` button is clicked', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('Quincy', { force: true });

    cy.contains('Username is available');

    cy.get('@usernameForm').contains('Save').click({ force: true });
    cy.contains('Account Settings for Quincy').should('be.visible');

    cy.resetUsername();
  });

  it('Should show flash message showing username has been updated', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('nhcarrigan', { force: true });
    cy.contains('Username is available');
    cy.get('@usernameInput').type('{enter}', { force: true, release: false });

    cy.contains('We have updated your username to nhcarrigan')
      .should('be.visible')
      // We are checking for classes here to check for proper styling
      // This will be replaced with Percy in the future
      .should(
        'have.class',
        'flash-message alert alert-success alert-dismissable'
      );

    cy.resetUsername();
  });

  it('Should be able to close the shown flash message', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('bjorno', { force: true });
    cy.contains('Username is available');
    cy.get('@usernameInput').type('{enter}', { force: true, release: false });

    cy.contains('We have updated your username to bjorno').within(() => {
      cy.get('button').click();
    });

    cy.contains('We have updated your username to bjorno').should('not.exist');

    cy.resetUsername();
  });

  it('Should change username if enter is pressed', () => {
    goToSettings();
    cy.get('@usernameInput')
      .clear({ force: true })
      .type('symbol', { force: true });
    cy.contains('Username is available');

    cy.get('@usernameInput').type('{enter}', { force: true, release: false });

    cy.contains('Account Settings for symbol').should('be.visible');

    cy.resetUsername();
  });
});
  
```

O código acima testa todo o processo de troca de um username feita por um usuário. Antes de cada teste é necessário acessar a página de configurações, portanto foi feita uma função para padronizar esse processo. Primeiramente testamos digitar um novo nome de usuário no campo apropriado, nesse caso "twaha". Em seguida checamos se a mensagem de validação aparece enquanto o username digitado é validado. Depois testamos os dois casos possíveis, caso o nome digitado esteja disponível ou não. 
Caso o username esteja disponível devemos testar o botão de salvar, que deve estar disponível para click neste caso. No caso de um username não estar disponível testamos se o botão de salvar está indisponível para click, como deve estar.

Testamos também o caso de o usuário digitar um caractere invalido, no caso: "Quincy Larson". Caso isso aconteça devemos informar o aviso de erro adequado para o usuário. Em seguida checamos se o botão de Salvar permanece desativado no caso de caractere inválido, como deveria permanecer.

Para o próximo teste checamos se a mudança foi bem sucedida após salvar um novo username (caracteres em caixa alta são considerados como diferentes). Digitamos "quincy", clicamos no botão de salvar e checamos se o nome da página de configurações para o usuário foi alterado para o novo nome. Também testamos para o caso do usuário apertar enter no teclado ao invés de clicar em salvar. Em seguida fazemos o mesmo teste de salvar, só que com o username "Quincy", para saber se o username será devidamente alterado.

freeCodeCamp/cypress/integration/settings/certifications.js 

``` javascript

import '@testing-library/cypress/add-commands';

describe('Settings certifications area', () => {
  describe('initially', () => {
    before(() => {
      cy.exec('npm run seed');
      cy.login();
    });

    it('Should render the default settings page', () => {
      cy.visit('/settings/');
      cy.findAllByText('Claim Certification').should($btns => {
        expect($btns).to.have.length(15);
      });
      cy.findByText('Show Certification').should('not.exist');
      cy.contains('Agree');
      cy.contains('Claim Certification').click();
      cy.contains(
        'To claim a certification, you must first accept our academic honesty policy'
      );
    });
  });

  describe('after isHonest', () => {
    before(() => {
      cy.exec('npm run seed');
      cy.login();
    });

    it('Should update the user as they try to claim their certifications', () => {
      cy.visit('/settings');
      cy.contains('Agree').click();
      cy.contains('You have accepted our Academic Honesty Policy.');
      cy.contains('Claim Certification').click();
      cy.contains(
        'It looks like you have not completed the necessary steps. Please complete the required projects to claim the Responsive Web Design Certification'
      );
    });
  });
});

```

O código acima testa a visualização de certificações na página de configurações do usuário. Primeiramente visitamos a página de configurações. Caso o usuário clique no botão de aceitar os termos, o botão de "claim certification" deve permanecer permanecer disponível para ser clicado. Caso o usuário não clique no botão de aceitar os termos, deve aparecer um aviso de orientação.
