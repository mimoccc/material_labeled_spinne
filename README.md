Depends on AppCompat Library, will publish my own appCompatLibrary soon

Simly:

public class SpinnerWithLabel extends AppCompatAutoCompleteTextView implements AdapterView.OnItemSelectedListener {
    protected AdapterView.OnItemSelectedListener listener;
    private Object selected_item;

    public SpinnerWithLabel(Context context) {
        super(context);

        init(context, null, 0);
    }

    public SpinnerWithLabel(Context context, AttributeSet attrs) {
        super(context, attrs);

        init(context, attrs, 0);
    }

    public SpinnerWithLabel(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);

        init(context, attrs, defStyleAttr);
    }

    private void init(Context context, AttributeSet attrs, int defStyleAttr) {
        setKeyListener(null);

        SpinnerWithLabel.this.dismissDropDown();

        setOnTouchListener(new OnTouchListener() {
            @Override
            public boolean onTouch(View view, MotionEvent e) {
                if (e.getAction() == MotionEvent.ACTION_DOWN) {
                    if (isPopupShowing()) {
                        SpinnerWithLabel.this.dismissDropDown();
                    } else {
                        SpinnerWithLabel.this.showDropDown();
                    }

                    return true;
                }
                return false;
            }
        });
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
}

