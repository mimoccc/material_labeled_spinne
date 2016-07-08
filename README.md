Depends on AppCompat Library, will publish my own appCompatLibrary soon.

Simply:

```javascript
public class AppCompatLabeledSpinner extends AppCompatAutoCompleteTextView implements AdapterView.OnItemSelectedListener {
    protected AdapterView.OnItemSelectedListener listener;
    private Object selected_item;

    public AppCompatLabeledSpinner(Context context) {
        super(context);

        init(context, null, 0);
    }

    public AppCompatLabeledSpinner(Context context, AttributeSet attrs) {
        super(context, attrs);

        init(context, attrs, 0);
    }

    public AppCompatLabeledSpinner(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);

        init(context, attrs, defStyleAttr);
    }

    private void init(Context context, AttributeSet attrs, int defStyleAttr) {
        setKeyListener(new KeyListener() {
            @Override
            public int getInputType() {
                return 0;
            }

            @Override
            public boolean onKeyDown(View view, Editable text, int keyCode, KeyEvent event) {
                if ((event.getKeyCode() == KeyEvent.KEYCODE_BACK) && isPopupShowing()) {
                    dismissDropDown();
                }
                return true;
            }

            @Override
            public boolean onKeyUp(View view, Editable text, int keyCode, KeyEvent event) {
                return true;
            }

            @Override
            public boolean onKeyOther(View view, Editable text, KeyEvent event) {
                return true;
            }

            @Override
            public void clearMetaKeyState(View view, Editable content, int states) {
            }
        });

        AppCompatLabeledSpinner.this.dismissDropDown();

        setOnTouchListener(new OnTouchListener() {
            @Override
            public boolean onTouch(View view, MotionEvent e) {
                if (e.getAction() == MotionEvent.ACTION_DOWN) {
                    if (isPopupShowing()) {
                        AppCompatLabeledSpinner.this.dismissDropDown();
                    } else {
                        AppCompatLabeledSpinner.this.showDropDown();
                    }

                    return true;
                }
                return false;
            }
        });

        setOnItemSelectedListener(this);

        addTextChangedListener(new TextWatcher() {
            public void afterTextChanged(Editable s) {
            }

            public void beforeTextChanged(CharSequence s, int start, int count, int after) {
            }

            public void onTextChanged(CharSequence s, int start, int before, int count) {
                setSelectionFromText();
            }
        });
    }

    @Override
    protected void onFocusChanged(boolean focused, int direction, Rect previouslyFocusedRect) {
        if (focused) {
            hideKeyboard(AppCompatLabeledSpinner.this);
        }
    }

    @Override
    public boolean enoughToFilter() {
        return false;
    }

    @Override
    public <T extends ListAdapter & Filterable> void setAdapter(T adapter) {
        adapter.registerDataSetObserver(new DataSetObserver() {
            @Override
            public void onChanged() {
                super.onChanged();

                if (getText().toString().length() == 0) {
                    onAdapterChange();
                }
            }
        });

        super.setAdapter(adapter);

        onAdapterChange();
    }

    private void onAdapterChange() {
        ListAdapter adapter = AppCompatLabeledSpinner.this.getAdapter();

        if ((adapter == null) || (adapter.getCount() == 0)) {
            setText(null);
        } else {
            Object o = (adapter == null) ? null : adapter.getItem(0);

            setText((o == null) ? null : o.toString());

            setSelectionFromText();
        }
    }

    private void setSelectionFromText() {
        ListAdapter adapter = AppCompatLabeledSpinner.this.getAdapter();

        if (adapter != null) {
            int i = getSelectionFromText(getText().toString());

            if (i > -1) {
                selected_item = adapter.getItem(i);
            } else {
                selected_item = null;
            }

            if (listener != null) {
                if (selected_item == null) {
                    onNothingSelected(null);
                } else {
                    onItemSelected(null, this, i, adapter.getItemId(i));
                }
            }
        }
    }

    private int getSelectionFromText(String s) {
        int ret = -1;

        if (s != null) {
            ListAdapter adapter = AppCompatLabeledSpinner.this.getAdapter();

            if ((adapter != null) && (adapter.getCount() > 0)) {
                for (int i = 0; i < adapter.getCount(); i++) {
                    Object o = adapter.getItem(i);

                    if (o != null) {
                        if (s.contentEquals(o.toString())) {
                            ret = i;

                            break;
                        }
                    }
                }
            }
        }

        return ret;
    }

    @Override
    public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
        selected_item = getAdapter().getItem(i);

        if (listener != null) {
            listener.onItemSelected(adapterView, view, i, l);
        }
    }

    @Override
    public void onNothingSelected(AdapterView<?> adapterView) {
        if (listener != null) {
            listener.onNothingSelected(adapterView);
        }
    }

    public AdapterView.OnItemSelectedListener getSelectionListener() {
        return listener;
    }

    public void setSelectionListener(AdapterView.OnItemSelectedListener listener) {
        this.listener = listener;
    }

    public Object getSelectedItem() {
        return selected_item;
    }

    private void hideKeyboard(View view) {
        InputMethodManager imm = (InputMethodManager) view.getContext().getSystemService(Context.INPUT_METHOD_SERVICE);
        imm.hideSoftInputFromWindow(view.getWindowToken(), 0);
    }
}


