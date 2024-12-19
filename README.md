<xml xmlns="http://www.w3.org/1999/xhtml">
  <variables>
    <variable type="" id="Stake" islocal="false" iscloud="false">Stake</variable>
    <variable type="" id="Take_Profit" islocal="false" iscloud="false">Take Profit</variable>
    <variable type="" id="Stop_Loss" islocal="false" iscloud="false">Stop Loss</variable>
    <variable type="" id="Switch_Counter" islocal="false" iscloud="false">Switch Counter</variable>
    <variable type="" id="Should_Change" islocal="false" iscloud="false">Should Change</variable>
  </variables>
  
  <block type="trade_definition" id="trade_logic" deletable="false" x="0" y="60">
    <statement name="TRADE_OPTIONS">
      <block type="trade_definition_market" id="market_setup">
        <field name="MARKET_LIST">synthetic_index</field>
        <field name="SUBMARKET_LIST">random_index</field>
        <field name="SYMBOL_LIST">1HZ10V</field>
        <next>
          <block type="trade_definition_tradetype" id="trade_type">
            <field name="TRADETYPECAT_LIST">digits</field>
            <field name="TRADETYPE_LIST">overunder</field>
            <next>
              <block type="trade_definition_contracttype" id="contract_type">
                <field name="TYPE_LIST">DIGITUNDER</field>
                <next>
                  <block type="trade_definition_candleinterval" id="candle_interval">
                    <field name="CANDLEINTERVAL_LIST">60</field>
                    <next>
                      <block type="trade_definition_restartonerror" id="restart_on_error">
                        <field name="RESTARTONERROR">TRUE</field>
                      </block>
                    </next>
                  </block>
                </next>
              </block>
            </next>
          </block>
        </next>
      </block>
    </statement>
    
    <statement name="INITIALIZATION">
      <block type="variables_set" id="initialize_stake">
        <field name="VAR" id="Stake" variabletype="">Stake</field>
        <value name="VALUE">
          <block type="math_number" id="stake_value">
            <field name="NUM">1</field>
          </block>
        </value>
        <next>
          <block type="variables_set" id="initialize_take_profit">
            <field name="VAR" id="Take_Profit" variabletype="">Take Profit</field>
            <value name="VALUE">
              <block type="math_number" id="profit_value">
                <field name="NUM">2</field>
              </block>
            </value>
            <next>
              <block type="variables_set" id="initialize_stop_loss">
                <field name="VAR" id="Stop_Loss" variabletype="">Stop Loss</field>
                <value name="VALUE">
                  <block type="math_number" id="loss_value">
                    <field name="NUM">30</field>
                  </block>
                </value>
                <next>
                  <block type="variables_set" id="initialize_switch_counter">
                    <field name="VAR" id="Switch_Counter" variabletype="">Switch Counter</field>
                    <value name="VALUE">
                      <block type="math_number" id="counter_value">
                        <field name="NUM">0</field>
                      </block>
                    </value>
                    <next>
                      <block type="variables_set" id="initialize_should_change">
                        <field name="VAR" id="Should_Change" variabletype="">Should Change</field>
                        <value name="VALUE">
                          <block type="logic_boolean" id="change_value">
                            <field name="BOOL">TRUE</field>
                          </block>
                        </value>
                      </block>
                    </next>
                  </block>
                </next>
              </block>
            </next>
          </block>
        </next>
      </block>
    </statement>

    <statement name="SUBMARKET">
      <block type="trade_definition_tradeoptions" id="trade_options">
        <field name="DURATIONTYPE_LIST">t</field>
        <value name="DURATION">
          <shadow type="math_number_positive" id="duration_value">
            <field name="NUM">1</field>
          </shadow>
        </value>
        <value name="AMOUNT">
          <block type="variables_get" id="amount_value">
            <field name="VAR" id="Stake" variabletype="">Stake</field>
          </block>
        </value>
        <value name="PREDICTION">
          <block type="math_number" id="prediction_value">
            <field name="NUM">8</field>
          </block>
        </value>
      </block>
    </statement>
  </block>

  <block type="before_purchase" id="before_purchase_logic" x="0" y="852">
    <statement name="BEFOREPURCHASE_STACK">
      <block type="procedures_callnoreturn" id="volatility_switcher">
        <mutation name="Volatility Switcher"></mutation>
        <next>
          <block type="apollo_purchase" id="purchase_action">
            <field name="PURCHASE_LIST">DIGITUNDER</field>
          </block>
        </next>
      </block>
    </statement>
  </block>

  <block type="after_purchase" id="after_purchase_logic" x="874" y="60">
    <statement name="AFTERPURCHASE_STACK">
      <block type="controls_if" id="profit_loss_check">
        <mutation elseif="1" else="1"></mutation>
        <value name="IF0">
          <block type="logic_compare" id="check_profit">
            <field name="OP">GTE</field>
            <value name="A">
              <block type="total_profit" id="profit_value_check"></block>
            </value>
            <value name="B">
              <block type="variables_get" id="profit_limit">
                <field name="VAR" id="Take_Profit" variabletype="">Take Profit</field>
              </block>
            </value>
          </block>
        </value>
        <statement name="DO0">
          <block type="set_tp" id="take_profit_action"></block>
        </statement>
        <value name="IF1">
          <block type="logic_compare" id="check_loss">
            <field name="OP">LTE</field>
            <value name="A">
              <block type="total_profit" id="loss_value_check"></block>
            </value>
            <value name="B">
              <block type="variables_get" id="loss_limit">
                <field name="VAR" id="Stop_Loss" variabletype="">Stop Loss</field>
              </block>
            </value>
          </block>
        </value>
        <statement name="DO1">
          <block type="set_sl" id="stop_loss_action"></block>
        </statement>
        <statement name="ELSE">
          <block type="trade_again" id="retry_trade"></block>
        </statement>
      </block>
    </statement>
  </block>
</xml>
